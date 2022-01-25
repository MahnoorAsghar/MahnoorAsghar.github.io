---
layout: page
title: "Modifying Expectations"
subtitle: ""
date: 2022-01-25 11:53:00 +0500
categories: ["Outreachy"]
---

<h3>Initial Outlook</h3>
Hello readers! I am now halfway through my Outreachy internship! (For an overview or reminder of what my project is about, please refer to my <a href="https://mahnoorasghar.github.io/outreachy/2022/01/14/Improving-Ironic's-REST-API-Documentation-mechanism.html">previous blog post</a>). 
During the Outreachy application process and in the beginning of my internship, I was spending some time reading about my project’s requirements and looked up existing solutions that targeted the API documentation use-case. Since the Ironic code base is in python, my search was scoped to finding frameworks and solutions that provided a means to generate documentation for a Python API.

There seemed to be degrees to how automatic this could be. Some solutions were fully automatic - they would parse the code and generate documentation about the available API methods, the methods’ parameters and return values (along with their data types).
This provided a uniform bird’s eye view into the API methods available, but there seemed to be little support for adding descriptions for the methods and their parameters. The generated documentation also looked like boilerplate output. From an API user’s perspective, there was a lot of information that was missing from the auto-generated API documentation. It was unfriendly, and difficult to understand at a glance.

<h3>A simple plan</h3>
The alternative was to generate the documentation from docstrings. This seemed like a better approach, since it would allow us to add descriptions for the API methods and their parameters, and fulfill the original goal of moving the documentation closer to the code. Ironic uses the Sphinx framework to generate its documentation currently, and I was delighted to discover that Sphinx supported docstring handling through an extension called autodoc! A simple plan emerged in my head - set up a sphinx environment alongside Ironic, and move the documentation from files to docstrings! I roughly calculated how much time I would need to revise each module’s documentation and move it into docstrings; and used that as a rough timeline for my Outreachy application. I had a rough idea of how the internship was going to go, and I was ready for it!

<h3>Responding to trouble</h3>
After spending a few weeks setting up my environment and testing out autodoc - I realized that the autodoc extension was missing key features that were required for our use case. We needed to be able to supply parameter descriptions in one place, and link those into the many modules that provided the API functionality. 

Along with that, it would also be useful to be able to add example requests and responses for each API method available. The example requests and responses would be in JSON format, and would come from external files that would have to be rendered into the generated documentation!

On the surface, autodoc was perfect for our use case, but that was not quite all that was needed to fulfill the Ironic API documentation use case. This was a surprise to me, as I had spent quite a bit of time setting up the extension and getting it to run; and only after inspecting its supposed input more closely, did I realize that it did not have the ability to process everything we desired.

There was not much time to be wasted. I discussed my findings with my mentor, and she encouraged me to probe further and try to find a way to develop the missing features - either in my own extension, or into autodoc itself. 


<h3>As the fog cleared</h3>
Fast forward about 2 weeks, and it became clear (through discussion) that the features should be provided by an independent extension. I started working on it, and was led to the docutils library to help me parse an input field list. The functionality seemed to be right there, but I tried 3 different approaches, only to get incorrect results. This was also appearing to be going differently from what I envisioned. I persisted in my efforts, trying to find some way to extract the field correctly, only to have it overwritten by the incorrect values every time! The lack of documentation and examples online only seem to convey to me the importance of developing a robust API documentation framework for Ironic xD

Ultimately, discussing the problem with my mentor and a screen sharing session later, I found that a simple regex pattern matching exercise will work. There was no explicit need to use the docutils classes to parse the input field list - that was not the only way forward! I merrily re-oriented myself, and continued working on the project, hoping to get at least one (of the three required) features delivered to satisfy the Ironic REST API documenters’ needs, in such a way that it is straightforward to extend the extension’s functionality further, and its code upholds all the OpenStack code quality standards. 

The practical work I have done so far did not precisely go as per the timeline I had originally proposed whilst applying for this project. However, it is important to keep the objective in sight, and work with the community to try and implement the best possible solution to suit the most important requirements. In practical settings, the path to implementation may change as we work on the problem, gather more project context, learn more about the involved technologies, and discuss desired solutions with users. The vision may evolve as we move forward, and it helps to modify our expectations accordingly!
