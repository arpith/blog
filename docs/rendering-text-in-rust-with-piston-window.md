---
layout: post
date: 2016-08-09 10:51:00 ET
title: "Rendering text in Rust with Piston-Window"
---

# Rendering Text in Rust with Piston-Window

I’m building a browser in Rust at the Recurse Center, and to do that I need to render text! Here’s how you do it with Piston-Window.

### Install

Add [piston_window](https://github.com/PistonDevelopers/piston_window) and [find_folder](https://github.com/PistonDevelopers/find_folder) to your [Cargo.toml](http://doc.crates.io/guide.html#adding-dependencies-from-cratesio)

    [dependencies]
    piston_window = "*"
    find_folder = "*"

### Create a window

My browser is full screen!

    extern crate piston_window;
    extern crate find_folder;

    use piston_window::*;

    pub fn render(hostname: String) {
        let hostname_str = &hostname[..];
        let mut window: PistonWindow = WindowSettings::new(
                hostname_str,
                [200, 200]
            )
            .fullscreen(true)
            .exit_on_esc(true)
            .build()
            .unwrap();

### Use a cool font

[find_folder](https://github.com/PistonDevelopers/find_folder) is pretty handy to get your assets folder.

        let assets = find_folder::Search::ParentsThenKids(3, 3)
            .for_folder("assets").unwrap();
        let ref font = assets.join("FiraSans-Regular.ttf");
        let factory = window.factory.clone();
        let mut glyphs = Glyphs::new(font, factory).unwrap();

### Render your text!

It’s finally time to render your text! Here’s how you do this:

        while let Some(e) = window.next() {
            window.draw_2d(&e, |c, g| {
                let transform = c.transform.trans(10.0, 100.0);
                // Set a white background
                clear([1.0, 1.0, 1.0, 1.0], g);
                text::Text::new_color([0.0, 0.0, 0.0, 1.0], 32).draw(
                    hostname_str,
                    &mut glyphs,
                    &c.draw_state,
                    transform, g
                );
            });
        }
    }

And that’s it! Building something cool? [Get](http://github.com/arpith) [in](http://twitter.com/arpith) [touch](mailto:arpith@feedreader.co)!
