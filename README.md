# Java Memory Management: Stack-Heap Diagram Exercise

## Overview
This repository contains the solution for a Java memory management exercise where the task is to create a step-by-step Stack-Heap Diagram. The purpose of the exercise is to understand how variables and objects are managed in the JVM memory model (stack and heap) during the execution of a Java program.

## Exercise Details

### Code Overview
The program consists of a main method and two user-defined methods:
- `main()`: Initializes variables, calls the `fill()` method, and prints the result.
- `fill()`: Modifies the input `times` value based on the string length and adds elements to the collection.
- `shrink()`: Shrinks the given string by picking every second character.

Here is the Java code:

```java
public class Main {
    public static void main(String[] args) {
        String name = "Kevin";
        List<String> list = new ArrayList<>();
        int times = 10;
        System.out.println(times + fill(list, name + name, times));
    }

    public static int fill(Collection<String> collection, String str, int times){
        String shrunk = shrink(str);
        times = (times + shrunk.length()) / 2;
        for (int i = 0; i < times / 2; i++) {
            collection.add(shrunk);
        }
        return times;
    }

    public static String shrink(String str){
        int newLength = str.length() / 2 + str.length() % 2;
        char[] chars = new char[newLength];
        for (int i = 0; i < str.length(); i+=2) {
            chars[i / 2] = str.charAt(i);
        }
        return new String(chars);
    }
}
```

### **Step 1: Start of main method**

Stack:

    name = "Kevin" (a string variable that points to an object in the heap).
    list = new ArrayList<>() (an empty list, which will point to an object in the heap).
    times = 10 (an integer variable).
Heap:

    The string "Kevin" — an object in the heap, referenced by the name variable.
    A new ArrayList object (an empty list), referenced by the list variable.

### **Step 2: Call to fill(list, name + name, times)**
    The string name + name creates the string "KevinKevin".
Stack:

    The variable str = "KevinKevin", which points to a new object in the heap.
    A new object shrunk is created inside the fill method:
    A call to shrink("KevinKevin").

### Step 3: Call to shrink(str)
Stack:

    str = "KevinKevin" (the input parameter for the shrink method).
    Local variables:
    newLength = 10 (calculated as half the length of the string).
    chars = new char[5] (a character array of length 5).
Heap:

    The chars array is placed in the heap.
    The string "KevinKevin" is processed, creating a new string:
    chars[0] = 'K'
    chars[1] = 'v'
    chars[2] = 'i'
    chars[3] = 'e'
    chars[4] = 'i'
    A new string object "Kviei" is created in the heap and returned by the shrink method.
    Stack after returning from shrink:
    shrunk = "Kviei" (the variable now references a new string object in the heap).

### Step 4: Continuing the fill method
    After returning from shrink, the fill method continues.
    times = (times + shrunk.length()) / 2 = (10 + 5) / 2 = 7.
    In the loop, the string "Kviei" is added 3 times to the list:
    Stack: Local variables are created for each iteration of the loop:
    The i variable for the loop index.
    Each iteration adds a reference to the "Kviei" string object into the list.
Heap:

    The list object now contains references to the "Kviei" string. Three copies of the string "Kviei" are added to the list.
    Step 5: Completion of fill and main methods
    The fill method returns 7, and the result is printed:
    Stack: After the return from fill, the stack is cleared of temporary variables.
    The expression System.out.println(times + fill(list, name + name, times)); adds 10 + 7, printing 17.

## Final Memory State

### Stack:
    Variables:
    name, list, times in the main method.
    str, times, shrunk in the fill method.
    str, newLength, chars in the shrink method.
    The variable i in the loop in the fill method.
    After method calls, the stack is cleared of local variables.
### Heap:
    The string object "Kevin" (referenced by name).
    The string object "KevinKevin" (result of name + name).
    The string object "Kviei", created by shrink.
    The ArrayList<String> (referenced by list).
    Three string objects "Kviei" added to the list.


### Stack:
    +----------------------------------+
    | main()                           |
    |   name = "Kevin"                 |
    |   list = <ArrayList>              |
    |   times = 10                      |
    +----------------------------------+
    | fill(Collection, String, int)    |
    |   str = "KevinKevin"             |
    |   times = 7                      |
    |   shrunk = "Kviei"               |
    |   (loop: i = 0, 1, 2)            |
    +----------------------------------+
    | shrink(String)                   |
    |   str = "KevinKevin"             |
    |   newLength = 10                 |
    |   chars = <char[5]>              |
    +----------------------------------+

### Heap:
    +-------------------------------------------+
    | "Kevin"                                   |
    +-------------------------------------------+
    | "KevinKevin"                              |
    +-------------------------------------------+
    | "Kviei"                                   |
    +-------------------------------------------+
    | ArrayList<String>                         |
    |   [ "Kviei", "Kviei", "Kviei" ]          |
    +-------------------------------------------+


## Memory State Diagrams
1. Initialization Phase

        [ STACK ]                          [ HEAP ]
        ┌──────────────────┐              ┌──────────────────┐
        │ main()           │              │ String "Kevin"   │
        │  name ───────────┼─────────────►│ (5 chars)        │
        │  list ───────────┼────┐         └──────────────────┘
        │  times = 10      │    │         ┌──────────────────┐
        └──────────────────┘    └───────►│ ArrayList []     │
        └──────────────────┘

2. During fill() Execution
    
    
        [ STACK ]                          [ HEAP ]
        ┌──────────────────┐              ┌──────────────────┐
        │ fill()           │              │ String "Kevin"   │
        │  collection ─────┼────┐         ├──────────────────┤
        │  str ────────────┼──┐ │         │ String           │
        │  times = 7       │  │ │         │ "KevinKevin"     │
        │  shrunk ─────────┼─┐│ │         ├──────────────────┤
        └──────────────────┘ ││ │         │ String "KvnKv"   │
        ││ │         ├──────────────────┤
        [ STACK ]            ││ │         │ ArrayList        │
        ┌──────────────────┐ ││ │         │ ["KvnKv",        │
        │ main()           │ ││ │         │  "KvnKv"]       │
        │ (variables       │ ││ │         └──────────────────┘
        │  unchanged)      │ ││ │
        └──────────────────┘ ││ │
        ││ │
        [ STACK ]            ││ │
        ┌──────────────────┐ ││ │
        │ shrink()         │ ││ │
        │  str ────────────┼─┘│ │
        │  newLength = 5   │   │ │
        │  chars ──────────┼───┘ │
        └──────────────────┘     │
        │
        [ STACK ]                │
        ┌──────────────────┐     │
        │ (loop iterations)│     │
        │  i = 0,1,2       │     │
        └──────────────────┘     │
        │
        [ STACK ]                │
        ┌──────────────────┐     │
        │ char[5] array    │     │
        │ (during          │     │
        │  construction)   │     │
        └──────────────────┘     │
        │
        [ OUTPUT ]               │
        Final output: 17         │
        (10 + 7 from fill)       │
        └───────────────────────┘
