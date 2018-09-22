---
layout: post
date: 2018-02-24 20:24:00 ET
title: "Type checking Ruby with minimal effort"
---

# Type checking Ruby with minimal effort

This past week I took part in a mini retreat at the Recurse Center. I worked on generating type annotations for Ruby, heavily inspired by Instagram Engineering’s MonkeyType project (check out this blog post!).

### Motivation

There is a fun tool for adding types and type checking to Ruby called [RDL](https://github.com/plum-umd/rdl). [This video](https://www.youtube.com/watch?v=buY54I7mEjA) explains it well! In RDL, types can be used to decorate methods:

    require 'rdl'
    extend RDL::Annotate

    type '(Integer) -> String'
    def id(x)
      "forty-two"
    end

Running this code will throw an error if either the arguments or return type differs from the signature.

### Examples

I thought it would be fun to automatically add the annotations, and it is! Given an input file called input.rb that looks like this:

    def the_answer()
      return 'forty-two
    end

    the_answer()

Running kurangu input.rb updates it to look like:

    require 'rdl'
    require 'types/core'

    extend RDL::Annotate
    type '() -> String'
    def the_answer()
      return 'forty-two'
    end

    the_answer()

It gets more interesting when you have an input file that looks like:

    def the_answer(x)
      if x
        return 'forty-two'
      else
        return 42
      end
    end

    the_answer(true)
    the_answer(false)
    the_answer(nil)
    the_answer("string")
    the_answer(42)

kurangu input.rb adds the following annotation:

    require 'rdl'
    require 'types/core'

    extend RDL::Annotate
    type '(TrueClass or FalseClass or NilClass or String or Fixnum) -> String or Fixnum'
    def the_answer(x)
      if x
        return 'forty-two'
      else
        return 42
      end
    end

    the_answer(true)
    the_answer(false)
    the_answer(nil)
    the_answer("string")
    the_answer(42)

### How it works

Kurangu uses [TracePoints](http://ruby-doc.org/core-2.5.0/TracePoint.html) to [listen to method calls and returns](https://github.com/arpith/kurangu/blob/master/lib/trace.rb), and collect the runtime types for the arguments and return values.

    trace_call = TracePoint.new(:call) do |t|
      if File.dirname(t.path) == File.dirname(INPUT_FILE)
        s = "#{t.defined_class}, :#{t.method_id}"
        args = t.binding.eval("local_variables").inject({}) do |vars, name|
          value = t.binding.eval name.to_s
          vars[name] = value.class
          vars
        end
        stack[s] << args
      end
    end

Then, using the [method’s parameter](http://ruby-doc.org/core-2.5.0/Method.html#method-i-parameters) information, it creates a union of types for each parameter and updates the annotations file.

    trace_return = TracePoint.new(:return) do |t|
      if File.dirname(t.path) == File.dirname(INPUT_FILE)
        s = "#{t.defined_class}, :#{t.method_id}"
        args = stack[s].pop
        if args
          args.each do |arg, type|
            parameter_types[s][arg].add(type)
          end
          return_types[s].add(t.return_value.class)
          parameter_list[s] = t.self.method(t.method_id).parameters.map { |a | a[1] }
          line = t.self.method(t.method_id).source_location[1]
          signatures[s] = generate_signature(line, parameter_list[s], parameter_types[s], return_types[s])
          path = "#{t.path}.annotations"
          dir = File.dirname(t.path)
          write_annotations_paths(dir, paths.add(path))
          write_annotations(path, signatures)
        end
      end
    end

Note that it only generates annotations for files in the same directory as the input file. This is to avoid (for example) annotating the type checker itself! A future version could support including other directories.

The code that [generates the annotation](https://github.com/arpith/kurangu/blob/f7236b2823446e2016663a1db7bcc309d5674eb2/lib/trace.rb#L12-L15) is very straightforward:

    def generate_signature(line, parameters, parameter_types, return_types)
      joined_parameters = parameters.map { |arg| parameter_types[arg].to_a.join(" or ") }
      "#{line} type '(#{joined_parameters.join(", ")}) -> #{return_types.to_a.join(" or ")}'"
    end

Once the [annotation file](https://github.com/arpith/kurangu/blob/master/test/the_answer_falsy/input.rb.annotations) is generated with line numbers and annotations, the input file is read line by line and if there is a generated annotation for the line it is inserted (if there is an existing annotation it is replaced).

    lines = []
    has_types = false
    File.open(original_path, "r") do |f|
      f.each_line.with_index do |line, index|
        whitespace = line.chomp(line.lstrip)
        if annotations.key?(index + 1)
          if lines.last and lines.last.start_with?('type')
            has_types = true
            lines.last = "#{whitespace}#{annotations[index + 1]}"
          else
            lines << "#{whitespace}extend RDL::Annotate\n"
            lines << "#{whitespace}#{annotations[index + 1]}"
          end
        end
        lines << line
      end
    end
    if !has_types
      lines.unshift "require 'types/core'\n\n"
      lines.unshift "require 'rdl'\n"
    end
    IO.write(annotated_path, lines.join())

Note that the white space is copied 😎

Overall, this was a super fun week! If you’ve been considering applying to the Recurse Center, you should definitely try it! It’s even better than you’ve heard 😊

Thanks to [Kamal Marhubi](undefined), [Robert Hönig](https://twitter.com/theindielives), [Rishi Ghosh](https://github.com/rgho), [Ann Kidder](http://www.a-tbd.com/), [Connor Walsh](https://github.com/connorwalsh) and [Danielle Pham](https://twitter.com/quelledanielle) for helping me figure things out!
