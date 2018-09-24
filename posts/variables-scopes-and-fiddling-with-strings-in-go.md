---
layout: post
date: 2014-11-21 12:51:00 GMT
title: "Variables, Scopes and fiddling with Strings in Go"
---
# Variables, Scopes and fiddling with Strings in Go

<p>Some more fun with strings! I&#8217;m finally weaning <a href="http://arpith.co/post/102944903612/stumbling-through-go">myself off</a> <a href="http://golang.org/pkg/fmt">Sscanf</a> and onto <a href="http://golang.org/pkg/encoding/hex/"><code>encoding/hex</code></a>, so this time I&#8217;ll decode the original string with</p>

<pre><code>    h := os.Args[1]
    bytes, e := hex.DecodeString(h)
    if e != nil {
            panic(e)
    }
</code></pre>

<p>Note that while the parentheses (around <code>e != nil</code>) <a href="https://gobyexample.com/if-else">aren&#8217;t required, the brackets are</a>. That page also mentions</p>

<blockquote>
  <p>A statement can precede conditionals; any variables declared in this statement are available in all branches.</p>
</blockquote>

<p>Which made me wonder, where are variables available? The <a href="http://www.golang-book.com">Golang Book</a>'s page <a href="http://www.golang-book.com/4/index.htm">on variables says</a></p>

<blockquote>
  <p>According to the language specification “Go is lexically scoped using blocks”. Basically this means that the variable exists within the nearest curly braces { } (a block) including any nested curly braces (blocks), but not outside of them.</p>
</blockquote>

<p>And the <a href="https://golang.org/ref/spec#Declarations_and_scope">spec explains</a></p>

<blockquote>
  <p>Go is lexically scoped using <a href="https://golang.org/ref/spec#Blocks">blocks</a>:</p>
  
  <ol><li>The scope of a <a href="https://golang.org/ref/spec#Predeclared_identifiers">predeclared identifier</a> is the universe block.</li>
  <li>The scope of an identifier denoting a constant, type, variable, or function (but not method) declared at top level (outside any function) is the package block.</li>
  <li>The scope of the package name of an imported package is the file block of the file containing the import declaration.</li>
  <li>The scope of an identifier denoting a method receiver, function parameter, or result variable is the function body.</li>
  <li>The scope of a constant or variable identifier declared inside a function begins at the end of the ConstSpec or VarSpec (ShortVarDecl for short variable declarations) and ends at the end of the innermost containing block.</li>
  <li>The scope of a type identifier declared inside a function begins at the identifier in the TypeSpec and ends at the end of the innermost containing block.</li>
  </ol></blockquote>

<p>Looking for <a href="https://golang.org/ref/spec#Short_variable_declarations">ShortVarDecl</a> and I found</p>

<blockquote>
  <p>Short variable declarations may appear only inside functions. In some contexts such as the initializers for &#8220;if&#8221;, &#8220;for&#8221;, or &#8220;switch&#8221; statements, they can be used to declare local temporary variables.</p>
</blockquote>

<p>Which took me to the section on <a href="https://golang.org/ref/spec#If_statements">if statements</a></p>

<blockquote>
  <p>The expression may be preceded by a simple statement, which executes before the expression is evaluated.</p>
</blockquote>

<p>Looks like it&#8217;s <a href="https://golang.org/ref/spec#SimpleStmt">not just variable declarations</a>:</p>

<blockquote>
  <p>SimpleStmt = <a href="https://golang.org/ref/spec#EmptyStmt">EmptyStmt</a> | <a href="https://golang.org/ref/spec#ExpressionStmt">ExpressionStmt</a> | <a href="https://golang.org/ref/spec#SendStmt">SendStmt</a> | <a href="https://golang.org/ref/spec#IncDecStmt">IncDecStmt</a> | <a href="https://golang.org/ref/spec#Assignment">Assignment</a> | <a href="https://golang.org/ref/spec#ShortVarDecl">ShortVarDecl</a> .</p>
</blockquote>

<p>Back to the strings though, I&#8217;m interested in how many times each character appears in a string. The <a href="http://blog.golang.org/strings">blog post on strings</a> that I linked to in my <a href="http://arpith.co/post/102944903612/stumbling-through-go">last post</a> talks about using a <a href="https://golang.org/ref/spec#RangeClause"><code>range</code> clause</a></p>

<blockquote>
  <p>For a string value, the &#8220;range&#8221; clause iterates over the Unicode code points in the string starting at byte index 0. On successive iterations, the index value will be the index of the first byte of successive UTF-8-encoded code points in the string, and the second value, of type rune, will be the value of the corresponding code point. If the iteration encounters an invalid UTF-8 sequence, the second value will be 0xFFFD, the Unicode replacement character, and the next iteration will advance a single byte in the string.</p>
</blockquote>

<p>So I&#8217;m going to store a count for every <code>rune</code> I encounter, and a map looks fine for that. <a href="http://blog.golang.org/go-maps-in-action">This blog post on maps</a> mentions</p>

<blockquote>
  <p>If the requested key doesn&#8217;t exist, we get the value type&#8217;s zero value. In this case the value type is int, so the zero value is 0</p>
</blockquote>

<p>I&#8217;ve also imported <a href="http://golang.org/pkg/unicode/">unicode</a> because I&#8217;m only interested in letters and don&#8217;t care what case they are in. I happen to be keeping track of non-useful characters so I have a score (a float(64!) because I&#8217;m going to have to do a little math later).</p>

<pre><code>    score := 0.0
    charCount := make(map[rune]int)
    for _, char := range plaintext {
            if unicode.IsLetter(char) {
                    if !unicode.IsLower(char) {
                            char = unicode.ToLower(char)
                    }
                    charCount[char] += 1
            } else if char != ' ' {
                    score += 1
            }
    }
</code></pre>

<p>Specifically, I&#8217;m looking at the frequencies of common English letters, and comparing it with <a href="http://en.wikipedia.org/wiki/Letter_frequency">what you might expect to get</a> (<a href="http://golang.org/pkg/math/#Abs">math.Abs</a> handles <a href="http://golang.org/pkg/builtin/#float64">float64</a>'s)</p>

<pre><code>    score += math.Abs(length*freq/100 - float64(charCount[char]))
</code></pre>

<p>And that&#8217;s about it for today.</p>