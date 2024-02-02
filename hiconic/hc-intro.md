# Hiconic

> This is a mixture of my thoughts and experiences developing _Hiconic_. For purely technical information see the [project's GitHub](https://github.com/hiconic-os).

## Overview

`Hiconic` is a technology that arose from consequent application of modeling, i.e. representing all kinds of things as data.

It offers a lot of convenience for common use-cases (e.g. serialization), makes writing generic components simpler and the normalization of treating everything as data leads to high code reusability.

It consists of multiple layers. On the lowest level it extends the language with a new _entity_ type to contain data (as _class_ contains code), on the highest level offers a modular (web)application framework, with many components and tools in between.

This document briefly explains what it is and tries to give insights into why it has value.

Links at the bottom provide more details on various aspects, including non-technical behind the scenes of why the company behind this tech failed.


## _Hiconic_ stack overview

_Hiconic_ consists of:
* **core** - a way a language extension (_Java_) with new type (_entity_) to purely represent data (as classes are for code).
* **modular web platform** - an alternative to popular frameworks like _Spring Boot_, but model-driven.
* **generic web client** - a web app to configure the server and manage application data
* **JavaScript library** which contains a **core** implementation for **JavaScript** and also provides a convenient way to exchange data (_entities_) with the server
* **Eclipse plugins for developers**

The modular platform comes with a list of modules which provide common features like **SQL persistence** (via Hibernate), **REST endpoint**, **GraphQL integration** and more.


## _Hiconic_ core

**The core is by far the most important part of the stack.**

The type system for modeling, consisting of _entities_ and _enums_ (and collections thereof), comes with its own **reflection** for easy generic handling. Based on the reflection come many tools for standard tasks, e.g.:
* **serialization** - XML/JSON/YAML/binary
* **cloning** - making shallow/deep/custom-configured copies
* **meta-data** - possibility to annotate (attach additional information to) types/properties with meta data and to resolve it
* **AOP** - interception of data manipulation (e.g. getter/setter invocation), which allows features like tracking changes or lazy loading

As for more advanced features:
* **Smood** is our own in-memory object oriented database with its own query language similar to HQL, and persistence. This offers an alternative to SQL DBs which supports free modeling with multiple inheritance and polymorphic properties, at least for cases where the data fits in memory.
* **GMML** a language to describe data manipulations optimized for quick parsing


## Examples

Few examples to get an idea...

### Entity declaration:

_GenericEntity_ is a super-type of all entities.
```java
public interface Person extends GenericEntity {
    EntityType<Person> T = EntityTypes.T(Person.class);

    String getFirstName();
    void setFirstName(String firstName);

    String getLastName();
    void setLastName(String lastName);

    Date getBirthDate();
    void setBirthDate(Date birthDate);
}
```

### Generic processing:

Entity reflection (_EntityType<>_, _Property_) is much more convenient and faster than Java reflection (_Class<>_, _Method_).
```java
public void print(GenericEntity entity) {
    EntityType<?> type = entity.entityType();
    System.out.println("Type: " +  type.getShortName());

    for (Property p: type.getProperties()) {
        String name = p.getName();
        String value = p.get(entity);

        System.out.println(name +  ": " + value);
    }
}
```

### Metadata Resolution:

Assuming Icon is a custom metadata type defined on a type fur UI purposes.
```java
public Icon getIcon(GenericEntity entity) {
    return mdResolver.getMetaData()
                .entity(entity)
                .metaData(Icon.T)
                .exclusive();
}
```

```java
public boolean isMandatory(GenericEntity entity, String propertyName) {
    return mdResolver.getMetaData()
                .entity(entity)
                .property(propertyName)
                .is(Mantatory.T);
}
```

### Modeled APIs (DDSA):

We call this **Denotation Driven Service Architecture (DDSA)** - API calls are made by evaluating request entities:

```java
public String resolveLastWatchedMovieName(Person person) {
    const request = ResolveLastWatchedMovieName.T.create();
    request.setPerson(person);

    Movie movie = request.eval(this.evaluator).get();
    return movie.getName();
}
```

In JS/TS the property access looks very natural.
```typescript
public async resolveLastWatchedMovieName(person: Person): Promise<string> {
    const request = ResolveLastWatchedMovieName.create();
    request.person = person;

    const movie = await request.EvalAndGet(this.evaluator);
    return movie.name;
}
```


## Advantages

Many of the advantages are discussed elsewhere, so we just sum them up grouped:

### Core
* ease of writing generic code
* many data manipulation tools out of the box
* high component reusability
* AOP for data manipulation

### Modeling APIs (DDSA)
* separation of concerns - code doesn't know who implements its dependencies
* normalization of API calls - just evaluate a request

### JS/TS integration
* sharing data types between client and server
* server calls almost identical to Java
* generic client out of the box


## What is missing?

While the tech is promising, there seem to be quite a few blockers that prevent developers from giving it a chance.

### Monolith

The web application framework is designed for a modular monolith, not microservices.

This should not be a big deal in practice, microservices are not a silver bullet and they often introduce more complexity than they solve, but it's the current trend and you have to accept that.

Interestingly though, creating an alternative, microservices oriented framework would be quite easier without all kinds of features of our monolith.

### Eclipse

_Eclipse_ was a great, free, open source IDE when we started developing the technology, so once we realized we could use some IDE plugins, we went for it.

_IntelliJ IDEA_ has gained a lot of "market share" since, with a lot of religious following for some reason. I've tried both and while _IntelliJ_ was slightly better, the difference wasn't that huge. Not to mention a big number of developers don't even use many of its features. But hey, customer is always right. Though to be fair, many tools including _GitHub Copilot_ are (still) not available for _Eclipse_, which should be a wake up call for the _Eclipse_ community.

If we had the resources, we would definitely add support for _IntelliJ_, it doesn't need much. Maybe future LLMs will make this much easier 🤞

### JavaScript/TypeScript Support

While there is a great _JavaScript_ library (with type declarations for _TypeScript_), the integration is a bit clumsy. Most notably there is no way to define a model (Entities) in JS. One has to create a model in _Java_ and add it as a dependency to the JS/TS project.

This works fine for full stack developers, and makes sense for models shared between server and client, but there is a clear path towards having a pure _Node.js_ implementation where this would have to be addressed.

## Further Details

* [Convoluted Company History](./hc-history.md)
* [Why should anyone care about modeling stuff?](./hc-my-contributions.md)
* [My personal contributions to the project](./hc-my-contributions.md)

