rtt_rosdeployment
-----------------

This package provides an easy way to interact with an Orocos deployment
component over ROS. This uses ROS srv types in the
[rtt_ros_msgs](../rtt_ros_msgs) package.

## Supported Operations (C++ : ROS Service Name)

* `DeploymentComponent::runScript`:`run_script`
* `DeploymentComponent::getPeerList`:`get_peer_list`
* `DeploymentComponent::eval`:`eval`

## Usage

You can advertise the services from a deployment component like the following:

```ruby
import("rtt_rosdeployment")
loadService("Deployer","rosdeployment") # or loadService("this","rosdeployment")
```
;;
