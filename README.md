# AgileStateMachine
Arduino/C++ library to simply and efficiently model and run a finite state machine.

___
### Introduction

**States** represent the different situations in which the machine can be at any time for given inputs.

A **Transition** connect two states, and are associated with a boolean trigger condition.

To update the machine, call the `execute()` function, in order to checks transitions of current running state: if one of the transitions is triggered, the machine goes into the next state.

```cpp
/* The Finite State Machine */
StateMachine fsm;

```

![SFC_esempio](https://user-images.githubusercontent.com/27758688/125982036-0eab0bb2-ed13-4101-af5c-6e49e82908fd.png)

### State definition and callback functions
Each states can be binded up to three different callback functions that will be executed when the state is activated (on enter), when is left (on exit) and while is running (on run). 

For each state it is also possible to define an optional maximum and a minimum duration time.

If a callback function is not needed, simply use value `nullptr` or `NULL` instead of the name of function.

```cpp
State* stExampleState = fsm.addState("An Example State", onEnter, onRun, onExit);
State* stAnotherState = fsm.addState("Another State", onEnter, nullptr, nullptr);

/* Set initial state and start the Machine State */
fsm.setInitialState(stExampleState);
fsm.start();
```

### Transition definition and trigger
To connect two states, a transition need to be defined. The trigger of transition can be performed with a `void function(bool &)` or a `bool` variable. 
Also the timeout of state itself can be used for triggering to next state. 

```cpp
stExampleState->addTransition(stAnotherState, aCallbackFunction);    // A callback function will be used as trigger
stAnotherState->addTransition(stAnotherState, aBoolVariable);        // A bool variable will be used as trigger
stAnotherState->addTransition(stGateClose, aTime);                   // An unsigned long variable will be used as timeout trigger
```

While running the State Machine, a State timeout can be checked with the method `bool getTimeout();`

``` cpp
if(fsm.getCurrentState()->getTimeout) {....}
```

### Action definition
For each state you can define also a set of qualified **Actions**, that will be executed when state is active causing effect to the target bool variable

```cpp
bool targetVar1, targetVar2;
stExampleState->addAction(Action::Type::N, targetVar1);
stAnotherState->addAction(Action::Type::R, targetVar2);
```

The library actually support this action qualifiers:

| Action qualifier | Description | Short explanation on the effect | Duration |
| :---: | :---: | :--- | :---: |
| N | Non-stored | Action is active (target = TRUE) as long as the state input is active | NO |
| R | Reset | Reset to FALSE the value of target variable | NO |
| S | Set (or Stored) | Set to TRUE the value of target variable | NO |
| L | time Limited | target = TRUE until the end of the set time or until the state is deactivated  | YES |
| D | time Delayed | target = TRUE  after the set time has elapsed until the state is deactivated  | YES |

### Examples

Take a look at the examples with some "scholastic" problems solved with a state machine in the [examples folder](https://github.com/cotestatnt/AgileStateMachine/tree/main/examples):
 - [Blinky.ino](https://github.com/cotestatnt/AgileStateMachine/blob/master/examples/Blinky)
 - [PedestrianLight.ino](https://github.com/cotestatnt/AgileStateMachine/tree/master/examples/PedestrianLight)
 - [AutomaticGate](https://github.com/cotestatnt/AgileStateMachine/blob/master/examples/AutomaticGate)
 - [RailCRossing.ino](https://github.com/cotestatnt/AgileStateMachine/blob/master/examples/RailCrossing)

<div style="content: flex">
<img src="https://user-images.githubusercontent.com/27758688/125971825-ed89b51d-6441-474b-86a5-063bc1f96770.png" width="480">
<img src="https://user-images.githubusercontent.com/27758688/125979422-31ad3912-eb93-406f-9b41-6f32c30b02ba.png" width="450">
</div>


### Constructor

```c++
// Create new Finite State Machine
StateMachine fsm;
```

### Public methods of `StateMachine` class
```c++

// Add a State to State Machine
State* addState(const char *name);
State* addState(const char *name, uint32_t min, uint32_t max);
State* addState(const char *name, state_cb enter = nullptr, state_cb run = nullptr, state_cb exit = nullptr);
State* addState(const char *name, uint32_t min = 0, state_cb enter = nullptr, state_cb run = nullptr, state_cb exit = nullptr);
State* addState(const char *name, uint32_t min, uint32_t max, state_cb enter = nullptr, state_cb run = nullptr, state_cb exit = nullptr);

// Start the State Machine
void start();

// Stop the State Machine
void start();

// Run the State Machine (true on transitions)
bool execute();

// Set first state (after start)
void setInitialState(State* state);

// Force to specific state
void setCurrentState(State *newState, bool callOnEntering = true, bool callOnLeaving = true);

// Get number of states 
const int GetStatesNumber();

// Get label of current state
const char *getActiveStateName();

// Get pointer to current state
State* getCurrentState();

// Get active state name
const char* ActiveStateName();

// Get the number of defined finite states
const int GetNumStates()
```

### Public methods of `State` class
```c++

// Add a transition to state
Transition* addTransition(State *out, bool &trigger);
Transition* addTransition(State *out, condition_cb trigger);
Transition* addTransition(State *out, uint32_t timeout);

// Add an action to state
Action* 	addAction(uint8_t type, bool &target, uint32_t _time = 0);

// Get the state index (the position as added in the linked list of StateMachine class)
uint8_t		  getIndex() ;

// True if state is running for a time greater then max time
bool 		    getTimeout() ;

// Set the max time for current state
void 		    setStateMaxTime(uint32_t _time);

// Set the min time for current state (before exit)
void 		    setStateMinTime(uint32_t _time);

// Reset the enter time (usefull for extend the running time over timeout)
void 	      resetEnterTime();

// Get the time when state has entered
uint32_t 	  getEnterTime();

// Get the state label name
const char*   getStateName();


```

### Supported boards
The library works virtually with every boards supported by Arduino framework (no hardware dependency)

+ 1.0.0 Initial version

