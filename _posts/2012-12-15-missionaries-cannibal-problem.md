---
title: "Missionaries and Cannibals problem breadth first search implementation"
date: 2012-12-15
tags: [java, algorithm]
excerpt: "java, algorithm"
---

Problem description:

   Three missionaries and cannibals want to cross the river using a boat which can carry at most two people, under the constraint that, for both banks, if there are missionaries present on the bank, they can't be outnumbered by cannibals (if they were, the cannibals would eat the missionaries.) The boat cannot cross the river by itself with no people on board.

Problem solution:

   This kinds of problem has many solutions. All the solution are all about state space search. For this problem the state represent is very important. The problem's state can be represented as three element tuple (M, C, B).

- M: number of missionaries on left bank
- C:  number of cannibals on left bank
- B:   number of boat on left bank 

   The state can be a six element tuple the left bank state and right bank state, because we can deduce the right bank state by left bank state, so the three left bank element tuple is enough. The start state is (3, 3, 1) which mean all the missionaries, cannibals and boat on the left side bank. The end state is (0, 0, 0) which means all the missionaries, cannibals and boat on the right bank. To find a path from the state space tree, we should skip or prune some invalid branches or nodes.

When generate the successive states, some states are not allowed except the below operation to get successive states.
 
1. Move one cannibal across the river.
2. Move two cannibals across the river.
3. Move one missionary across the river.
4. Move two missionaries across the river.
5. Move one missionary and one cannibal.

* Below is an implementation with breadth first search. Some other methods I will study later.

```java
package hello.test;

import java.util.LinkedList;
import java.util.Queue;

public class MCB3 {
	// the state queue used for search
	private Queue<State> q = new LinkedList<State>();

	// State class
	private class State {
		private int missionary;  // the number of  missionaries between 0 and 3
		private int cannibals;	// the number of cannibals between 0 and 3
		private int boat;	// the number of boat between 0 and 1
		private State previous;	// the previous state used to record the state transfer path
		private int stateLevel;	// state level used to record the height of the tree

		// constructor
		public State(int missionary, int cannibals, int boat, int stateLevel) {
			this(missionary, cannibals, boat, null, stateLevel);
		}

		// constructor
		public State(int missionary, int cannibals, int boat, State preState, int stateLevel) {
			this.missionary = missionary;
			this.cannibals = cannibals;
			this.boat = boat;
			this.previous = preState;
			this.stateLevel = stateLevel;
		}

		// get the state level
		public int getStateLevel() {
			return this.stateLevel;
		}

		// is the state a valid state
		public boolean isValid() {
			if (missionary < 0 || missionary > 3 || cannibals < 0 || cannibals > 3) return false;
			if(3 - missionary < 0 || 3 - missionary > 3 || 3 - cannibals < 0 || 3 - cannibals > 3) return false;
			if(missionary > 0 && cannibals > missionary) return false;
			if(3 - missionary > 0 && 3 - cannibals > 3 - missionary) return false;
			return true;
		}

		// is two states equal to each other
		public boolean isEqual(State s) {
			return (s.missionary == this.missionary &&
					s.cannibals == this.cannibals &&
					s.boat == this.boat);
		}

		// is the state is the goal state
		public boolean isGoal() {
			State goal = new State(0, 0, 0, 99999);
			return isEqual(goal);
		}

		// print the path
		public void print() {
			if(this.previous != null) {
				this.previous.print();
			}

			String side = this.boat == 1 ? " Boat Right -> " : " <- Boat Left ";
			System.out.println(this.missionary + "M/" + this.cannibals + "C " + side + "" +
			(3 - this.missionary) + "M/" + (3 - this.cannibals) + "C");
		}
	}

	// expand the successors from the current state and enqueue these successor states
	private void successors(State currentState) {
		for(int i = 0; i <= 2; i++) {
			for(int j = 0; j <= 2; j++) {
				if(i == 0 && j == 0) continue;
				if(i + j > 2) break;
				addStatetoQueue(currentState, i, j);
			}
		}
	}

	// generate a new state and enqueue the state
	private void addStatetoQueue(State preState, int missionary, int cannibal) {
		// TODO Auto-generated method stub
		int side = preState.boat == 1 ? -1 : 1;
		State state = new State(preState.missionary + side * missionary,
				preState.cannibals + side * cannibal, 1 - preState.boat, preState,
				preState.getStateLevel() + 1);
		if(state.isValid())
			q.add(state);
	}

	// solve the mcb problem with bfs
	public void solve() {
		//ArrayList<State> solutions = new ArrayList<State>();
		boolean isFirstFound = false;	// is the state first time found
		boolean isallFound = false;	// is all the states found
		int optimalLevel = 0;	// the state level
		int count = 0;	// solution count

		State start = new State(3, 3, 1, 1);	// start state
		q.add(start);

		while(!q.isEmpty() && !isallFound) {
			State current = q.remove();

			if(current.isGoal()) {
				if(isFirstFound) {
					if(current.getStateLevel() <= optimalLevel) {
						count++;
						System.out.println("The "+ count + "th solution ===");
						current.print();
						System.out.println();
					} else {
						isallFound = true;
					}
				} else {
					isFirstFound = true;
					optimalLevel = current.getStateLevel();
					count++;
					System.out.println("The "+ count + "th solution, the level is " + optimalLevel + " ======" );
					current.print();
					System.out.println();
				}

			} else {
				successors(current);
			}
		}
	}

	/**
	 * @param args
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		MCB3 mcb = new MCB3();
		mcb.solve();
	}

}

```
