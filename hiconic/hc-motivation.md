# Models, OK, BFD

**Consequent model-driven approach** is a hard sell, mostly because it's not obvious what its advantages are.

**Generic programming** is not routinely utilized as _Java_ reflection is quite inconvenient. 

_Hiconic_ brings both, so let's consider other examples with these two aspects and see if there's a lesson to learn.


## Functional programming in Java

With _Java 8_ the language was extended with syntax (and efficient implementation) for lambda expressions - snippets of code that can be passed around and evaluated at will.

This opened the floodgates to all kinds of API extensions, most notably _Streams_, which turned _Java_ from tedious and verbose garbage to quite elegant and nimble language, at least in some aspects.

Collection manipulations like filtering, mapping and grouping, which make up a huge chunk of many tasks, became much easier to write, the code became much more standard making it easier to maintain.

But IMHO the most interesting aspect of this revolution is the fact that this was all possible from the beginning. The _Stream_ API could have been defined as it is now already in _Java 1.1_, just using it would be less elegant with anonymous inner classes. OK, it would have been really ugly.

This shows how simplicity of certain patters determines whether they are used and I claim the same about generic data processing.

Let's consider our example that [prints an entity type and all its properties](./hc-intro.md#generic-processing). That code can be slightly simplified to:
```java
public void print(GenericEntity entity) {
    EntityType<?> type = entity.entityType();
    System.out.println("Type: " +  type.getShortName());

    for (Property p: type.getProperties())
        System.out.println(p.getName() +  ": " + p.get(entity));
}
```

Compare that to an equivalent POJO implementation using _Java_ reflection:
```java
public void print(Object pojo) {
    Class<? extends Object> clazz = pojo.getClass();
    System.out.println("Type: " +  clazz.getSimpleName());

    for (Method m : clazz.getMethods()) {
        String methodName = m.getName();
        if (!methodName.startsWith("get"))
            continue;

        String propName = methodName.substring(3);
        propName = propName.substring(0, 1).toLowerCase() + propName.substring(1);

        try {
            Object value = m.invoke(pojo);
            System.out.println(propName +  ": " + value);

        } catch (IllegalAccessException e) {
            throw new RuntimeException("I can't call a getter? What Now?", e);
        } catch (InvocationTargetException e) {
            throw new RuntimeException("What does this even mean?", e);
        }
    }
}
```

Exactly, you need a really good reason to do something like this.

With _Hiconic_'s model reflection you have a nimble tool to write all kinds of generic algorithms for data processing, cloning, traversing, validation, serialization and many more.


## Modern Algebra

> OK, this is not for everyone.

Modern algebra is a branch of algebra studying mathematical structures like groups, fields, vector spaces and so on. It is the theoretical base for coding theory, especially [Error Detection and Correction](https://en.wikipedia.org/wiki/Error_detection_and_correction) and cryptography.

These mathematical structures are abstractions of mathematical Objects we use daily, like whole numbers, real numbers or vectors. These abstractions are chosen as a set of the most important properties, e.g. whole numbers can be added together, the order doesn't matter (a+b=b+a), there is a neutral element (a+0=a), and each element has an inverse element, which if added yields the neutral element (a+(-a)=0). In algebraic language this means that whole numbers with standard addition form a _commutative group_.

At first glance this seems unnecessarily complicated. We need to define a lot of rules and news terms, while we could study the numbers directly. Everything we do with such groups, we can do with numbers directly.

This is 100% true, yet we still want to study the abstract structures instead.

First, vectors with standard addition are also a commutative group, so anything we discover on this abstract level immediately applies to both, just like all the other abstract structures stand for  many different concrete objects.

But on top of that, this abstraction and invention of new terms helps us focus. Giving ideas a name makes them easier to fully understand them, and makes building of more complicated structures on top manageable.

**It's a kind of magic that is both extremely powerful and extremely unintuitive.**

IMHO **the same holds true for model-driven system architecture**.

Just like theorems for algebraic structures hold for many different concrete object, algorithms and tools written for modeled data an be reused for modeled APIs/Errors/Configuration.

Writing generic algorithms for one domain yields better insight into possibilities in another domain. With enough experience you develop an intuition for modeling, and just notice how other people's confusions have straight forward answers.

I wish I could give a good example, but it's one of those things where you are aware of it when it happens, but then can't remember because you already see the world with new eyes. Same reason why a lot of experts are bad teachers.

I'll try to write a good example down next time I encounter it though :)

[Go back to main page](./hc-intro.md)

<!-- Khachaturian - Waltz from Masquerade -->
[Listen to great music (Youtube)](https://www.youtube.com/watch?v=Y7JxLgf3wLM)