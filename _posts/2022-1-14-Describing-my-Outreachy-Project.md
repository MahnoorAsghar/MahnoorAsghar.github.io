---
layout: page
title: "Describing my Outreachy project"
subtitle: ""
date: 2022-01-14 10:00:00 +0500
categories: ["Outreachy"]
---

Hello everyone!
In this post I will try to explain why there was a need to re-think the way Ironic's REST API documentation was being generated, which became my Outreachy project. 

Have you ever been in a situation where you tried to use a software or library you found online, and it seems to offer you exactly the functionality you require, in the language of your choice. The community looks active, and the project looks stable. But when you try to use it, there is little comprehensive material you can find to quickly help you get up and running. You try to understand it through release notes, googling, looking over the code a little. But you have a deadline, and the code is too complex to understand at a glance. All you needed was an overview of and details about the API. But you may end up abandoning the library as using it would require too much time and effort. 

It is important for users and developers to be able to access API and/or code documentation quickly and easily. And keeping it up to date is also important, as the project evolves and new features are constantly being developed or existing ones changed.

<h3>Current status</h3>
Currently, the Ironic API documentation is stored in a sub-directory in the Ironic repository, separate from the directory in which the API code is stored. This means that the developers need to remember to alter/add to the documentation in a place that is quite separate from the code, which means it often can be forgotten about. Ensuring that the documentation stays updated is a significant effort of sorts, especially when the project is open source and contributors from all over the world are simultaneously working on different parts of it. And we all know how easy it is to forget to update the docs in the excitement of implementation!

The goal is to move the documentation closer to the API code, so that it is easy to maintain and update. (Please refer to my <a href='https://mahnoorasghar.github.io/outreachy/2021/12/30/Everyone-Struggles.html'>previous blog post</a> for an overview of Ironic.) In order to work on my Outreachy project, I have had to learn about the existing documentation system being used by Ironic. The current documentation is being generated using a tool called <b>Sphinx</b>. Sphinx takes in text files (in <b>reStructuredText</b> or <b>markdown</b> format), and can generate documentation in a variety of formats - PDF, HTML, LaTeX and man pages. reStructuredText and Markdown are both <b>markup languages</b>. Markup languages are used to annotate an electronic document - give information about its structure, organize its contents into elements. It specifies how something should be displayed or what something means.

As we can see, in order to generate this documentation, there are a lot of moving parts performing various functions. Lets try to untangle this a little further, to get more insight into how, if at all, we can modify our use of Sphinx to ease the effort of maintaining its API documentation.

The input files are parsed, and Restructured Text (reST) files are generated from them. 
Sphinx uses ‘<b>docutils</b>’ (a python text-processing system) to convert the generated reST into the desired output format (e.g. PDF). Docutils converts input text (e.g. in reST format) into output formats (e.g. LaTeX, HTML etc). It is composed of many readers, parsers and writers, in order to parse and generate outputs in multiple formats.

<h3>Possible Solution</h3>
Since my project’s core problem is to move the documentation closer to the code, it would be a viable solution to move the documentation from the separate sub-directory to docstrings within the code files itself.

Sphinx has a built-in extension called ‘<b>autodoc</b>’ that generates documentation from docstrings. This seems perfect for our use, but there is one feature in the autodoc extension that is a bit wanting. Since the same parameter is being used in multiple API methods, across many modules, its description will have to be repeated everywhere in the docstrings. For example, a node object will have the same description everywhere it’s an API parameter. This description must be provided in the documentation, but it is tiresome and difficult to maintain the same definition consistently everywhere in code; wherever the node object is being passed as a parameter. If the description of the node object changes, it will have to be updated in all the docstrings for consistency and accuracy. 

One possible solution is to place the parameter descriptions in one file, and link the descriptions from that file into the docstrings.

The input docstring would then be:

Source code:
```
def sample_func(foo, bar, baz):
    """Some wonderful feature.

    Some good explanation of sample_func().
    
    .. rest_parameters:: parameters.yaml
       :foo: r_foo
       :bar: r_bar
       :baz: r_baz
    """
```

Output would be rendered as:
```
sample_func(foo, bar, baz)

  Some wonderful feature.

  Some good explanation of sample_func().
   
   Parameters:
      foo: <foo explanation>
      bar: <bar explanation>
      baz: <baz explanation>
```

I discussed this use case with the Sphinx community, in order to understand their landscape and how this problem can be approached best. The idea is to implement a new directive in the docstring, and use a field list in the directive body to describe the function parameters. Komiya Takesha from the Sphinx community was kind enough to offer me perspective on the scope of the autodoc extension, and answer some questions about the approach to implementation. He recommended placing the responsibility of linking and replacing the descriptions in the docstrings, in an independent Sphinx extension operating alongside autodoc. (<a href='https://groups.google.com/g/sphinx-dev/c/CZvdPao41yw/m/1zzqBWevCAAJ'>Link to discussion</a>) I think it is a good idea to use the existing framework because it provides a lot of the functionality we need, while allowing us to develop anything specific to our use case. It is also a good way to support another open source tool.  

The discussion proved very helpful, and I would love to hear and discuss any suggestions, alternate approaches, and general feedback from readers. If there is another rST-based approach that would be more feasible for a user/developer, or anything about how the extension should work, what the structure should be, etc. let’s discuss it in <a href='https://storyboard.openstack.org/#!/story/2009785'>the RFE (Request for Enhancement)</a> on the Ironic Storyboard.

