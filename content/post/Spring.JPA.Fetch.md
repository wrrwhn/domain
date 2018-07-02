---
title: "Spring.JPA.Fetch"
date: "2018-05-23"
categories:
 - "整理"
tags:
 - "Spring"
 - "JPA"
toc: true
---


# Setting
## **Default**
|              |    Type    | Setting |
|--------------|------------|---------|
| JPA 2.0 spec |            |         |
|              | OneToMany  | Lazy    |
|              | ManyToMany | Lazy    |
|              | OneToOne   | Eager   |
|              | ManyToOne  | Eager   |
| Hibernate    |            |         |
|              | *          | Lazy    |


## Docs
### Hibernate
>
By default, Hibernate uses lazy select fetching for collections and lazy proxy fetching for single-valued associations. These defaults make sense for most associations in the majority of applications.

### Jpa Spec
> JPA Spec assumes that in general most of the applications will require the singleton relations by default be eager, whereas multi value relations by default be lazy.

# Reference
## Docs
- [21.1.1. Working with lazy associations](https://docs.jboss.org/hibernate/orm/3.6/reference/en-US/html/performance.html#performance-fetching-lazy)

## Other
- [Why is the JPA FetchType EAGER by default for the @ManyToOne relationship?](https://stackoverflow.com/questions/27519399/why-is-the-jpa-fetchtype-eager-by-default-for-the-manytoone-relationship)
- [hibrenate @ManyToOne(fetch = FetchType.EAGER) 和 lazy 区别](https://my.oschina.net/u/1163293/blog/171343)
