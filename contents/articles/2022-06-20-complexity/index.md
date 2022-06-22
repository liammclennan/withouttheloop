---
title: Strategies for Managing Complexity
author: Liam McLennan
date: 2022-06-20 20:32
template: article.jade
---

> The most fundamental problem in computer science is problem decomposition: how to take a complex problem and divide it up into pieces that can be solved independently.

[says John Ousterhout](https://web.stanford.edu/~ouster/cgi-bin/book.php) and later...

# Modular Design

> The second approach to [eliminate] complexity is to encapsulate it, so that programmers can work on a system without being exposed to all of its complexity at once. This approach is called modular design.

By hiding complexity inside of modules we can reduce the complexity that we have to manage at any moment. The challenge of modular design is planning the interfaces of modules and composing them correctly. 

When done well, a module takes a complex implementation and reduces it to a simple API. Perhaps you work for a search engine and there is a `search` module with an API like:

```rust
search_web(search_phrase) -> search_results
```

The interface is simple, but we might expect the implementation to have some complexity. 

Modules may have dependencies on other modules, and may be dependencies of other modules. Thinking a little more about searching the web we realise that we first need a database of web content, so perhaps the API is actually:

```rust
search_web(web_content, search_phrase) -> search_results
```

And because that `web_content` database didn't assemble itself there might be another module responsible for that:

```rust
let web_content = web_crawler.crawl(known_starting_urls);
let search_results = search_engine.search_web(web_content, search_phrase);
```

The `search_engine` module probably depends on other modules for things like natural language parsing of the search phrase and the ranking of results. By factoring behaviour into cohesive modules we hope that the individual modules, and the consumers that compose them, can all deal with a limited amount of complexity. 

# General-Purpose is Better than Specialized

> [Over-specialization may be the single greatest cause of complexity in software](https://web.stanford.edu/~ouster/cgi-bin/aposd2ndEdExtract.pdf)

A general purpose module solves a general problem in a general way. Perhaps a Fourier transformer, or a JSON parser. A specialized module solves the exact problem you have right now. The trap is that specialized solutions feel like the responsible path that avoids unnecessary work, but general purpose code is more accessible, more reusable and easier to understand. 

At work we have spoken about designing components as though we intend to publish them as open-source software, even if we know we never will. That is nothing but a technique to prime ourselves to make our modules general-purpose. 




