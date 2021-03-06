---
permalink: /code/
title: "Code"
header:
  overlay_color: "#303030"
  overlay_image: /assets/images/texture.png
---

# <i class="fab fa-github"></i> Repositories
The main code repository is under [the EdgeVPNio organization in GitHub](https://github.com/edgevpnio), where you can also find [code releases and packages](https://github.com/EdgeVPNio/evio/releases)

Docker containers can be found in the [EdgeVPNio organization in DockerHub](https://hub.docker.com/u/edgevpnio)

# <i class="fas fa-user-plus"></i> How to Contribute
We welcome community involvement and participation in all aspects of the EdgeVPN.io project. There are several meaningful ways for external participants to contribute. Below we discuss the ways that we can benefit from your contributions and effective ways of going about this.

## <i class="fas fa-bug"></i> Finding and Submitting Bugs
If you encounter any bugs while testing, evaluating or in your everyday use the software, [please create a GitHub issue to report it](https://github.com/EdgeVPNio/evio/issues). Of course submitting the relevant information that will help us reproduce and diagnose the fault is important. If you run into errors/crashes, please proceed as follows:

* Set the [logging level in the configuration file to DEBUG](/configfile), and try to recreate the problem

* Retrieve the log files for controller and tincan - the file names are also specified in the configuration file

* Upload the log files to an accessible repository where we can retrieve them, and submit a GitHub issue

* Additionally, provide the EdgeVPN.io version, the platform being used, and configuration file(s) used (please ensure you remove sensitive information, such as XMPP and TURN user name/password)

## <i class="fas fa-plus"></i> Enhancements and Feature Suggestions
While using our software you may envision new and interesting ways that it can be used. As a research group we are always excited to hear these ideas - [please get in touch with us, let us know, and join the user's group and developer's Slack channel](/about). We have finite resources and cannot guarantee that any requested feature will be implemented, but others in the community could be interested. 
 
## <i class="fas fa-user"></i> How you use EdgeVPN.io
Tell us about how you use our software, particularly so if you think you are using it way we haven't explicitly documented. Additionally, if have a development dependency -- or you integrate our software with your own, we would love to hear from you.

## <i class="fas fa-book"></i> Updating the Wiki Documentation
We try to make keep our documentation in the Wiki current, accurate and succinct. If you encounter inaccuracies or ambiguities, please make an update and send us the pull request.

## <i class="fas fa-code"></i> Contributing Code
There is always more code to write than we can get to as quickly as we like. If you have an interest in Python, C++, SDN, and system level network development you may want to participate in developing some of new features of the software. 

We generally attempt to adhere the Google Style Guides for [C++](https://google.github.io/styleguide/cppguide.html) and [Python](https://google.github.io/styleguide/pyguide.html)

We have one master version of the software. This software product is tested and subjected to a high degree of code review. In order for code to make it into the master version, it must be submitted for code review. Once a project member other than the author has reviewed the code, the code is accepted into our testing branch. Once the code has passed a rigorous suite of tests, it is moved into the master branch. Periodically, packaged releases and containers are made from the master branch. 

## <i class="fas fa-box"></i> Porting and Packaging for new Platforms
We are also very interested in having EdgeVPN.io ported to as many platforms as possible. If you don't see a platform you use being supported, and would like to work on porting one, [please get in touch with us, let us know, and join the user's group and developer's Slack channel](/about).
