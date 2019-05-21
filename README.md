# HausmiPLY
## Plying is playing

C++ Development platform oriented to Security, Networking and Concurrency.

>  HausmiPLY is a descendant of a previous project <a href='https://sourceforge.net/projects/hausmisep/'>HausmiSEP</a>,
>  that was oriented to secure development and education but that didn't attract enough traction to be successful.
>
>  Learning from that experience, and with several years using the type of technology described, HausmiPLY was 
>  created as a simpler technology with more useful concepts and construction that can be used as building 
>  blocks for other projects.

## Introduction

The pre C++11 tradition has been to create deep hierarchies or to combine the basic C library programming style with object oriented elements, but was not until the functional extentions arrived that was possible to reimagine how to expand but without excesive abstraction, and without strange function pointer arithmetic.

With that capacity, is possible to integrate elements as concurrency, networking, data management and security, as a vital foundation for any modern software construction, without assembling parallel and isolated layers of libraries, classes or services that look as alien components on an heterogeneous mishmash, a craddle for huge security problems.

This aggregation approach focuses on pure functionality, offering a good tool for software designers to produce more products in less time by reusing in the process all possible previous work.  The abstraction process can be cataloged as a linear on, where the functionality can be traced directly to the lower levels and the only added elements are directly related with the abstraction and the language nature.

Notice the "security" foundation.  It is important to design for security not to believe that Security only falls into the category of  “exceptions”, dressed in all kind of patches.

The key is to work on the original purpose of abstraction: “to hide”.  But more than that,  security as part of software genes must be well crafted into every possible detail, no matter if it is a building element, a functional model or a layer.  What is not well designed, what is not well understood, what was not well built, is inevitably outside of the game.


### HausmiPly main goals:

* To build complex, data driven and networked products easier.
* To transform the way to construct software, more agile but also, more secure.
* Construct with highly compatible intelligent building elements, well crafted functional models and layers.
* Based on the “right” software patterns, applied uniformly on the overall software definition.
* All components with security embedded, without exception.
* All components created to be reusable, anti-obsolescence.
* Designed to grow in a sustainable way.
* Easy maintenance.

HausmiPLY work on the “layering” or “plying” concept. 

“Ply”, as a verb, means “to construct in a stable way”.  The idea is to create well defined layers of functionality, encapsulating domain specific operations and allowing other domains to interact through well defined access “windows” or designed openings.  These “windows”, however, are not simple interfaces (in the standard terminology).

```c++
EC::ResultCode hostLayer.window(guestLayer,data) {

    EC::ResultCode vRC = EC::Unknown;

    if (hostLayer.checkClearance(guestLayer,data).ok()) {

        hostLayer.lock();

        if (hostLayer.precondition(data).ok()) {
            if (guestLayer.worker(hostLayer.dataCopy(),data).ok()) {
                if (hostLayer.postcondition(data).ok()) {
                    vRC = hostLayer.dataUpdate();
                }
                else {
                    vRC = hostLayer.lastResult();
                    hostLayer.dataDiscard();
                }
            }
            else {
                vRC = guestLayer.lastResult();
            }
        }
        else {
            vRC = EC::FailureOnHostPrecondition;
        }
        hostLayer.unlock();
    }
    else {
        vRC = EC::WrongSecurityLevel;
    }
    return vRC;
}
```

The **host** “window” checks  on its own layer a “precondition” for the resources that an upper layer or **guest** is requesting, to delegate the **guest** “worker” execution to the **host** with its private data.  The **guest** has a particular “clearance” that must match with the set of resources it asks to work with; if the **host** layer determines that the **guest** is qualified and it can use the requested resources, then it will permit to the **guest** “worker” to perform its duty inside the **host** layer domain.

```c++
hostLayer.program(PRIVATEAREA,MAKECOPY);
```

The layer could create a copy of the requested resources, depending on a previous “program” operation that modifies the window behavior.  Meanwhile, the original resource is blocked from any other workers.  Then, the worker is executed within the layer domain with the copied data.

When the worker completes a valid execution and modifies the data it received, the layer will execute a “postcondition” on the worker's modified resource to determine if modifications are made following the layer standards.  If the standards were not followed, the layer will reject the worker's modifications, discarding the copy of the resources and unlocking the original ones.

If the postcondition is correctly accomplished, a valid acknowledge will be emitted and the worker's modifications will be applied within the layer domain.

As can be seen, the preconditions and postconditions are executed on the same layer where the “window” is located. 

* The layer is its resources owner.
* The layer is the expert on the usage of its resources.
* The layer has the responsibility on its resources.
* The layer is the only one that can approve the usage of its resources.
* The layer is the only one that can approve modifications on its resources.
* The layer window is the only place where other layers can modify its resources.

This model puts the management of resources in the hands of its owner, who knows all the 'business' of its resource and thus keep its resources under complete control.

The checking of clearance, precondition and postcondition are filters with default behaviors that can be replaced when “docking” other types of filters in their corresponding working place.  In this way, it is not necessary to create long inheritance hierarchies to perform all sort of tasks on windows, and a layer instance can be configured once to run the window many times during its life.

```c++
hostLayer.dock(CHECKCLEARANCE,accessLayerFilter);
hostLayer.dock(PRECONDITION  ,preConditionFilter);
hostLayer.dock(POSTCONDITION ,postConditionFilter);
```

As no model or implementation is perfect, it could be possible that a new security problem will arise in the future.  But software constructed with HausmiPLY approach will be able to identify the problem much faster as security is managed by layers, that are very ordered and each one has all sovereignty over its territory.  Not only it could greatly reduce the side effect or damage of such security problems, but to correct the problem won't need to disturb any other layers, and any improvement of the components become an organic part of the original system. 

This is, security crafted as the genes on any software construction.  Security will grow in the life of the software, not as patches, neither as part of an “exception” mechanism.
