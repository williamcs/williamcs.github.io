---
title: "Calculate Pi with Akka"
date: 2018-11-25
tags: [scala, akka]
excerpt: "scala, akka"
---

### Introduction
This is a simple tutorial to get started with Akka and Scala, I heavily refer to the old tutorial from akka document. With some changes to the latest version of Akka to get the program running.

The sample application that we will create is using actors to calculate the value of Pi. Calculating Pi is a CPU intensive operation and we will utilize Akka Actors to write a concurrent solution that scales out to multi-core processors. This sample will be extended in future tutorials to use Akka Remote Actors to scale out on multiple machines in a cluster.

We will be using an algorithm that is called “embarrassingly parallel” which just means that each job is completely isolated and not coupled with any other job. Since this algorithm is so parallelizable it suits the actor model very well.

Here is the formula for the algorithm we will use:
<img src="{{ site.url }}{{ site.baseurl }}/images/new/pi-formula.png" alt="linearly separable data">

In this particular algorithm the master splits the series into chunks which are sent out to each worker actor to be processed. When each worker has processed its chunk it sends a result back to the master which aggregates the total result.

### Create an sbt project
Below is the `build.sbt` file with akka libraries dependencies added:
There are some extra dependencies for other programs. The need of this pi calculating program is akka-actor.
```
name := "akka-quickstart-scala"

version := "1.0"

scalaVersion := "2.12.6"

lazy val akkaVersion = "2.5.16"

lazy val akkaKafkaVersion = "0.22"

libraryDependencies ++= Seq(
  "com.typesafe.akka" %% "akka-stream" % akkaVersion,
  "com.typesafe.akka" %% "akka-stream-testkit" % akkaVersion,
  "com.typesafe.akka" %% "akka-actor" % akkaVersion,
  "com.typesafe.akka" %% "akka-remote" % akkaVersion,
  "com.typesafe.akka" %% "akka-testkit" % akkaVersion,
  "com.typesafe.akka" %% "akka-stream-kafka" % akkaKafkaVersion,
  "org.scalatest" %% "scalatest" % "3.0.5" % "test"
)

```

Full code:
```scala
package com.tutorial.first

import akka.actor._
import akka.routing._

import scala.concurrent.duration.Duration

object Pi extends App {

  calculate(nrOfWorkers = 4, nrOfElements = 10000, nrOfMessages = 10000)

  sealed trait PiMessage

  case object Calculate extends PiMessage

  case class Work(start: Int, nrOfElements: Int) extends PiMessage

  case class Result(value: Double) extends PiMessage

  case class PiApproximation(pi: Double, duration: Duration)

  class Worker extends Actor {

    override def receive: Receive = {
      case Work(start, nrOfElements) =>
        sender ! Result(calculatePiFor(start, nrOfElements))
    }

    def calculatePiFor(start: Int, nrOfElement: Int): Double = {
      var acc = 0.0

      for (i <- start until (start + nrOfElement)) {
        acc += 4.0 * (1 - (i % 2) * 2) / (2 * i + 1)
      }

      acc
    }
  }

  class Master(nrOfWorkers: Int, nrOfMessages: Int, nrOfElements: Int, listener: ActorRef) extends Actor {

    var pi: Double = _
    var nrOfResults: Double = _
    val start: Long = System.currentTimeMillis

    val workerRouter = context.actorOf(Props[Worker].withRouter(RoundRobinPool(nrOfWorkers)), name = "workerRouter")

    override def receive: Receive = {
      case Calculate =>
        for (i <- 0 until nrOfMessages) workerRouter ! Work(i * nrOfElements, nrOfElements)
      case Result(value) =>
        pi += value
        nrOfResults += 1

        if (nrOfResults == nrOfMessages) {
          // Send the result to the listener
          listener ! PiApproximation(pi, duration = Duration(System.currentTimeMillis - start, "millis"))

          // Stop this actor and all its supervised children
          context.stop(self)
        }
    }
  }

  class Listener extends Actor {

    override def receive: Receive = {
      case PiApproximation(pi, duration) =>
        println("\n\tPi approximation: \t%s \n\tCalculation time: \t%s"
          .format(pi, duration))
        context.system.terminate()
    }
  }

  def calculate(nrOfWorkers: Int, nrOfElements: Int, nrOfMessages: Int): Unit = {
    // Create an Akka system
    val system = ActorSystem("PiSystem")

    // Create the result listener, which will print the result and shutdown the system
    val listener = system.actorOf(Props[Listener], name = "listener")

    // Create the master
    val master = system.actorOf(Props(new Master(nrOfWorkers, nrOfElements, nrOfMessages, listener)), name = "master")

    // Start the calculation
    master ! Calculate
  }

}

```

Running result:
```
Pi approximation: 	3.1415926435897883
Calculation time: 	586 milliseconds
```

Reference: [Getting Started Tutorial](https://doc.akka.io/docs/akka/2.0/intro/getting-started-first-scala.html)
