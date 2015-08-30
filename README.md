# buhtig

Scala client for **Github** rest API. The main goal is to be damn easy, and to adapt to changes on the API. In fact it is just a utility to help you build requests against github in a fluid way.

## why?

The idea is to have a basic utility to interact with [Github REST API](https://developer.github.com/), that does not require you to study a new API. If you already know github REST API you already know how to use this library, and because it is no more than a utility to help you build requests, if the API changes you don't need to wait for a new version to be released.

## Installation

### sbt

```
libraryDependencies += "net.caoticode.buhtig" %% "buhtig" % "0.1.0"
```

The library is cross compiled for scala 2.10 / 2.11

### sources

```bash
:> git clone https://github.com/mdread/buhtig.git
:> cd buhtig
:> sbt package
```

The generated jar file can be found under target/scala_{version} (don't forget to add [Dispatch](http://github.com/dispatch/reboot) as dependency).

To execute tests create a file named *github.token* under *src/test/resources* with a valid github api token.

## Usage

```scala
val buhtig = new Buhtig(token)
val github = buhtig.client

// get information about this repository ;)
github.repos.mdread.buhtig.sync

// same but with user and repo as parameters
github.repos("mdread", "buhtig").sync

// get future
github.repos(user, repo).async

// get json content as a string (syncronously)
github.repos("mdread", "buhtig").sync

// get an option containing the json response or None (syncronously)
github.repos("mdread", "buhtig").syncOpt

// adding query parameters
github.search.repositories ? ("q" -> "buhtig", "language" -> "scala") sync

// example of getting the content of a future with for-expressions
for(repo <- github.repos("mdread", "buhtig").async) {
    println(repo)
}

// close the client releasing threads and connections
buhtig.close
```

## How does it works?

Internally all http requests are managed by [Dispatch](http://github.com/dispatch/reboot). The external API is build around scala [Type Dynamic](http://docs.scala-lang.org/sips/completed/type-dynamic.html) functionality, which basically allows to intercept calls to missing methods, this is how you construct urls making method invocations.
