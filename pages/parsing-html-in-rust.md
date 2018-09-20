
# Parsing HTML in Rust

I’m building a browser at the Recurse Center, and as a first step, I need to parse HTML! Here’s how you do it in Rust:

## Get the Hostname

We’re going to read the hostname (for example, [https://medium.com)](http://facebook.com)) as a command line argument.

    fn main() {
        if let Some(arg1) = env::args().nth(1) {
            let hostname = arg1;

## Get the HTML

[Hyper](http://hyper.rs/) is a great HTTP library — here’s how you use it to make a GET request.

    let client = hyper::Client::new();
    let mut res = client.get(&hostname).send().unwrap();

## Parse the HTML

Use [html5ever](https://github.com/servo/html5ever)! It’s great!

    let dom = parse_document(RcDom::default(),
        Default::default()).from_utf8().read_from(&mut res).unwrap();

## Bonus: Find the CSS Links

To do this, we need to traverse the dom tree and looking for nodes that look like <link type=”text/css” href=”cssLink”>. When we find a css link, we push them into a vector of strings.

    fn get_css_links(handle: Handle) -> Vec<String> {
        let mut csslinks: Vec<String> = Vec::new();
        let mut queue: Vec<Handle> = Vec::new();
        queue.push(handle);
        while queue.len() != 0 {
            let handle = queue.remove(0);
            let node = handle.borrow();
            match node.node {
                Element(ref name, _, ref attrs) => {
                    assert!(name.ns == ns!(html));
                    let mut is_css = false;
                    for attr in attrs.iter() {
                        assert!(attr.name.ns == ns!());
                        let link = string_cache::Atom::from("link");
                        let type = string_cache::Atom::from("type");
                        let css = Tendril::from("text/css");
                        if name.local == link && 
                            attr.name.local == type && 
                            attr.value == css {
                                is_css = true;
                        }
                        let href = string_cache::Atom::from("href");
                        if is_css && attr.name.local == href {
                            let link = String::from(attr.value.clone());
                            csslinks.push(link);
                        }
                    }
                }
                _ => {
                    //don't do anything
                }
            }
            for child in node.children.iter() {
                queue.push(child.clone());
            }
        }
        return csslinks;
    }

That’s it! Rust is fun! Special thanks to [Kamal Marhubi](undefined)!

[Get](mailto:arpith@feedreader.co) [in](http://twitter.com/arpith) [touch](http://github.com/arpith) if you use Rust or if building a browser sounds interesting!
