---
title: "Data type convertion in java with reflection"
date: 2018-12-16
tags: [java]
excerpt: "java"
---

Sometimes we need to convert one data type to another data type, the common way is use getter
and setter method. It has some limitation, for example, at the first time, you convert TypeOne
to TypeTwo. After some time, you want to convert TypeOne to TypeThree, you must reimplement TypeOne
to TypeThree's method again, this seems not a very good idea. If the TypeOne to other data type can
be configured, that will be a better idea. With java's relection method, we can do this. The two data
type's relationship is defined in a map data structure. It can be easy implemented in config file, or
store the relations in db.

Java implementation:

```java
package com.test.type;

import java.lang.reflect.Method;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

public class TypeConverter {

    private static Map<String, TupleTwo<String, Class<?>>> map = new HashMap<> ();

    public static TypeTwo converter(TypeOne typeOne, String typeTwoClassName) throws Exception {
        Class typeTwoClass = Class.forName(typeTwoClassName);
        Object typeTwoInstance = typeTwoClass.newInstance();

        /*
        Method method = typeTwoClass.getDeclaredMethod("setIdentity", int.class);
        method.invoke(typeTwoInstance, typeOne.getClass().getMethod("getId").invoke(typeOne));

        method = typeTwoClass.getDeclaredMethod("setName", String.class);
        // below two methods are the same
        method.invoke(typeTwoInstance, typeOne.getClass().getMethod("getName").invoke(typeOne));
        method.invoke(typeTwoInstance, typeOne.getName());
        */

        for (Map.Entry<String, TupleTwo<String, Class<?>>> entry: map.entrySet()) {
            String key = entry.getKey();
            TupleTwo<String, Class<?>> value = entry.getValue();

            Method method = typeTwoClass.getDeclaredMethod(value.getValueOne(), value.getValueTwo());
            method.invoke(typeTwoInstance, typeOne.getClass().getMethod(key).invoke(typeOne));
        }

        return (TypeTwo) typeTwoInstance;
    }

    public static void main(String[] args) throws Exception {

        TypeOne typeOne = new TypeOne();

        typeOne.setId(1);
        typeOne.setName("typeOne");
        typeOne.setValue("valueOne");
        typeOne.setDate(new Date());

        // TypeOne with TypeTwo relation: id -> identity, name -> name, value -> extra, date -> createDate
        // map key is TypeOne's get method name, map value is TypeTwo's set method name and parameter type.
        map.put("getId", new TupleTwo<>("setIdentity", int.class));
        map.put("getName", new TupleTwo<>("setName", String.class));
        map.put("getValue", new TupleTwo<>("setExtra", String.class));
        map.put("getDate", new TupleTwo<>("setCreateDate", Date.class));

        String typeTwoClassName = "com.test.type.TypeTwo";

        TypeTwo typeTwo = converter(typeOne, typeTwoClassName);

        System.out.println(typeTwo.toString());
    }
}

/**
 * This is a Tuple2 implementation in java.
 * @param <O>
 * @param <T>
 */
class TupleTwo<O, T> {
    private O valueOne;
    private T valueTwo;

    public TupleTwo(O valueOne, T valueTwo) {
        this.valueOne = valueOne;
        this.valueTwo = valueTwo;
    }

    public O getValueOne() {
        return valueOne;
    }

    public void setValueOne(O valueOne) {
        this.valueOne = valueOne;
    }

    public T getValueTwo() {
        return valueTwo;
    }

    public void setValueTwo(T valueTwo) {
        this.valueTwo = valueTwo;
    }
}

class TypeOne {
    private int id;
    private String name;
    private String value;
    private Date date;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getValue() {
        return value;
    }

    public void setValue(String value) {
        this.value = value;
    }

    public Date getDate() {
        return date;
    }

    public void setDate(Date date) {
        this.date = date;
    }

    @Override
    public String toString() {
        return "TypeOne{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", value='" + value + '\'' +
                ", date=" + date +
                '}';
    }
}

class TypeTwo {
    private int identity;
    private String name;
    private Date createDate;
    private String extra;

    public int getIdentity() {
        return identity;
    }

    public void setIdentity(int identity) {
        this.identity = identity;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Date getCreateDate() {
        return createDate;
    }

    public void setCreateDate(Date createDate) {
        this.createDate = createDate;
    }

    public String getExtra() {
        return extra;
    }

    public void setExtra(String extra) {
        this.extra = extra;
    }

    @Override
    public String toString() {
        return "TypeTwo{" +
                "identity=" + identity +
                ", name='" + name + '\'' +
                ", createDate=" + createDate +
                ", extra='" + extra + '\'' +
                '}';
    }
}
```