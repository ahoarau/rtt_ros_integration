RTT Actionlib
=============

***NOTE: This is a preliminary design document and rtt\_actionlib is currently
unimplemented.***

Actionlib action servers provide the following interfaces, and actionlib action
clients reciprocate:
* Inputs:
  * Goal topic
  * Cancel topic
* Outputs: 
  * Status topic
  * Feedback topic
  * Result topic (per goal)

For an Orocos RTT component to provide an actionlib interface, it needs to
provide the topics described above. In order to expose actionlib interfaces
to components running in real-time threads, we should use the rtt data ports
to connect to these topics.

Contents
--------

This package provides an RTT service which constructs and connects several RTT
ports to a ROS actionlib action namespace.

### Providng Action Server from Orocos

In the standard ROS actionlib server, subscribing and publishing ROS messages on
the actionlib topics are handled automatiaclly by the ActionServer class, and
users are meant to delegate to an instance of this class. ActionServer then
calls short-running "goal" and "cancel" callbacks when it receives the
appropriate messages. It is in these user-supplied callbacks that goal
handlesare set to be accepted/rejected or preempted, respectively. 

The user sets the status of these handles by calling member functions
`setSucceeded()`, `setAborted()` etc. on the handles. These calls then call the
parent ActionServer's `publishResult()`,`publishStatus()` etc. member functions.
The "normal" ActionServer implements these functions with ROS publishers and
subscribers, but for RTT, we want these to use data ports.

To this end, we implement an RTTActionServer C++ class which inherits from
ActionServerBase and implements the "publish" functions with RTT data ports and
binds the goal and cancel callbacks to RTT event ports.

### Caling Action Servers outside of Orocos

TBD

Usage
-----


First the appropriate RTT ports need to be created in a given service (or
subservice) of a TaskContext. These are creted by delegating to an
RTTActionServer. The RTTActionServer will create the necessary RTT ports and
bind them to the user-supplied callbacks.

Second, the ports need to be connected to ROS topics. This can be done easily
with the "actionlib" service as shown below in Orocos .ops script:

```python
## Imports
import("rtt_ros");
ros.import("rtt_actionlib");

## Load some application-specific component
loadComponent("some_component_name","some_component_package::SomeComponent")

## Load the actionlib service
loadService("some_component_name","actionlib")

## Connect an actionlib server with goal/cancel/etc ports on the given service
some_component_name.actionlib.connect(
  "some_provided_service.sub_service",
  "/some/ros/namespace/my_action1")

## Connect an actionlib server with goal/cancel/etc ports on the task's root service
some_component_name.actionlib.connect("/some/ros/namespace/my_action2")
```

Alternatively, each data port could be streamed to the appropriate action
namespace manually.

Use Cases
---------

### Sending a Reference Trajectory to a Controller 



