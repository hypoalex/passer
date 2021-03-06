# Passer 
## A Low Latency Distributed Task Scheduler
[![Build Status](https://travis-ci.org/hypoalex/passer.svg)](https://travis-ci.org/hypoalex/passer)
[![Documentation Status](https://readthedocs.org/projects/passer/badge/?version=latest)](http://passer.readthedocs.org/en/latest)
[![Gitter chat](https://badges.gitter.im/hypoalex/passer.png)](https://gitter.im/hypoalex/passer)

# NOTE: THIS IS PRE-ALPHA QUALITY! #

This doesn't do anything yet. The Python gRPC implementation was beta last time 
I worked on this, as was version 3 of Protocol Buffers. This project couldn't be
reasonably used in a production system until both of these projects were
released, along with protobufs V3.

Now that they have been, I'm looking at this again!

This aims to be an implementation of [the Sparrow task scheduler design](sparrow.pdf)using Python 3.5 and [grpc](http://www.grpc.io/) instead of Java and Swift.
Documentation will use terminology from the design paper, so it is worth 
reading. Note that this is an idiomatic Python implementation and not a 1:1
port of [the Java Sparrow implementation](https://github.com/radlab/sparrow/),
but it is very true to the paper in most ways.

While this will initially be written in pure Python, it will eventually 
transition to using Cython once it is functionally complete and well-tested.
Exceeding the performance of Sparrow is a goal for the 1.0 release.

Passer aims to be an opinionated evolution of Sparrow, intended for use in a 
Docker-powered microservice environment making use of Consul for service 
discovery via DNS. The production deployment target is Joyent's Triton stack 
although it will work on any `DOCKER_HOST` or in any other environment, 
with appropriate customization. The use of Consul is also not required, 
although it is elegant and recommended.

While this is not Sparrow, it is definitely a member of 
[the Passer genus](https://en.wikipedia.org/wiki/Passer)! Most deviations from 
the design paper are a result of using Protocol Buffers instead of Swift, and I 
will try to document these changes in a separate document.

The [Motivation and Design Principles](http://www.grpc.io/blog/principles) of 
gRPC is a great resource to aid in understanding many aspects of this system. 
By virtue of using gRPC, usage of Passer is not limited to the Python 
ecosystem, and indeed it may be used from any language that offers bindings for 
[Google Protocol Buffers](https://developers.google.com/protocol-buffers/).
Thus, workers can be implemented using whatever language is most appropriate
for a given task. More than one worker can exist on a given compute node.

## Installation

This is pretty easy. You can just do `pip install passer`.

## Goals

1. To further reduce latency, it should be possible to stream messages between  scheduler front ends and workers. 
2. Port the Sparrow Spark implementation.


## Developing Passer

Passer *currently* requires Python 3.5 - provided you have it installed, you may do:

    make

This will prepare your tree for local development.

### Development Conventions

#### On Naming...

I try to stay true to the fairly standard naming convention that you see all 
over the place in Python and Ruby. Namely, `UpperCamelCase` for class names, 
`CAPITALIZED_WITH_UNDERSCORES` for constants, and `lowercase_with_underscores`
for other names. This convention is broken with Protocol Buffer definitions in 
order to stay true to the naming convention used in the design paper.

#### On Protocol Buffers...

All fields are marked optional in the .proto files and this is to ensure 
ongoing compatibility if this is used in production and the wire format evolves.
The entire concept of "required" fields is removed in protobufs 3.0.