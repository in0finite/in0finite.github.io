---
layout: post
title: Compiling Unity scripts into DLL - pros and cons
---

<br>

### pros:

- faster compilation ?
- source code is more-or-less hidden

<br>

### cons:

- limited debugging support
- can't rename scripts or namespaces (all references will be lost)
- requires special steps to configure for building (e.g. to postprocess NetworkBehaviours)
- you need multiple DLLs - one for Editor and one for builds ; sometimes separate DLL for each platform ;
- need to configure preprocessor defines - need to copy them from Unity and configure for each build target
- not much open-source friendly (harder for other people to modify code and contribute)


