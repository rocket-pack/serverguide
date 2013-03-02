Server Guide
===

How to read this guide:
---

This guide should be read sequentially - each link in this central document covers a different part of a server set up, however each section builds on what was achieved in the previous one. If you get lost, just return here, and take a look through the previous section until you find where everything last made sense.

If you find any problems - up to and including grammatical and spelling mistakes, please lodge an issue on [the Github project](https://github.com/joshmcarthur/serverguide/issues).

Introducing the example project
---

We are of course focusing on how to set up a server, but the purpose of the server is to efficiently serve up a Rails application - so, if you're just doing this to learn how, you're going to need a Rails application to deploy! I recommend my [Dogbook application](https://github.com/joshmcarthur/dogbook/tree/serverguide) - it's a very basic Rails application, specifically set up for prototyping new tools and technologies, that shouldn't have anything complex that will get in the way of you getting through this guide! 

If you are using your own application, and you run into problems with anything that you had to install or configure that wasn't listed here, please [tell us about it](https://github.com/joshmcarthur/serverguide/issues/new) - maybe it's something we need to build into this guide!

Sections
---

1. [First Steps & Assumptions](first-steps.md)
2. [Secure Access](secure-access.md)
3. [Install Utilities](utilities.md)
4. [Install database server](database-server.md)
5. [Memcached](memcached.md)
6. [Install Ruby](ruby.md)
7. [Install Passenger](passenger.md)
8. [Install Bundler](bundler.md)
9. [Configure deploy scripts](deployment.md)
10. [Configure monitoring](monitoring.md)
11. [Configure backups](backups.md)

Authors
---

* [Josh McArthur](https://github.com/joshmcarthur)

Licensing
---

This guide is published under a creative commons share-alike license. In simple terms, this means:

* You may take your own copy of this guide and modify it to fit your needs, so long as it remains under the same license as this guide (i.e. it must be free-to-access and modifyable in turn)
* If you do take your own copy, please **provide attribution** - this is extremely important, as I want anyone who experiences problems with this guide to know where to go for help.
* You may use this guide, and any modified copies for commercial and non-commercial purposes.

<a rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/deed.en_US"><img alt="Creative Commons License" style="border-width:0" src="http://i.creativecommons.org/l/by-sa/3.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" href="http://purl.org/dc/dcmitype/Text" property="dct:title" rel="dct:type">Server Guide</span> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/3.0/deed.en_US">Creative Commons Attribution-ShareAlike 3.0 Unported License</a>.<br />Based on a work at <a xmlns:dct="http://purl.org/dc/terms/" href="https://github.com/joshmcarthur/serverguide" rel="dct:source">https://github.com/joshmcarthur/serverguide</a>.



