## ROS Installation
* Run the following two commands:
```
echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

* These add the ROS environment setup script, setup.bash, to the current and future shells on this system. This means that commands and shell scripts provided by ROS, are now accessible to the shell interpreters on the system.

## ROS Graph
* A ROS system is made up of many different programs running simultaneously and communicating with one another by passing messages. The programs are the graph nodes, and programs that communicate with one another are connected by edges.
* Although things can get more complex, typically nodes are POSIX processes, and edges are TCP connections.
* ROS follows a publish/subscribe model.

## roscore
* Roscore is a service that provides connection information to nodes so that they can transmit messages to one another.
* Every node connects to roscore at startup to regis‐ ter details of the message streams it publishes and the streams to which it wishes to subscribe. When a new node appears, roscore provides it with the information that it needs to form a direct peer-to-peer connection with other nodes publishing and sub‐ scribing to the same message topics.
* Every ROS system needs a running *roscore*, since without it, nodes cannot find other nodes.
* Messages between nodes are transmitted peer-to-peer. The roscore is only used by nodes to know where to find their peers.
* When a ROS node starts up, it expects its process to have an environment variable named *ROS_MASTER_URI*. This variable is expected to contain a string of the form http://hostname:11311/, which in this case would imply that there is a running instance of roscore accessible on port 11311 somewhere on a host called hostname that can be accessed over the network.
* With knowledge of the location of roscore on the network, nodes register themselves at startup with roscore and then query roscore to find other nodes and data streams by name. Each ROS node tells roscore which messages it provides and which it would like to subscribe to. roscore then provides the addresses of the relevant mes‐ sage producers and consumers.
* roscore also provides a parameter server, which is used extensively by ROS nodes for configuration. The parameter server allows nodes to store and retrieve arbitrary data structures, such as descriptions of robots, parameters for algorithms, and so on. The parameter server can be reached using *rosparam*

## catkin
* catkin comprises a set of CMake macros and custom Python scripts to provide extra functionality on top of the normal CMake workflow.
* There are two files, CMakeLists.txt and package.xml, that you need to add some specific information to in order to have things work properly. You then call the various catkin tools to generate the directories and files you’re going to need as you write code for your robots.

## Workspaces
* A workspace is simply a set of directories in which a related set of ROS code lives. To make and init a new workspace do the following:
```
mkdir ~/catkin_ws/src
cd ~/catkin_ws/src
catkin_init_workspace
cd ..
catkin_make
```

* The catkin_init_workspace command creates a CMakeLists.txt file for you in the src directory, where you invoked it.
* cakin_make will create two new directories build and devel. Build contains libraries and executable programs and devel contains files and directories including setup files. It also has the setup.bash file.

## ROS Packages
* ROS software is organized into packages, each of which contains some combination of code, data, and documentation.
* Packages sit inside the workspaces inside the source directory. Each package directory must include a CMakeLists.txt file and a package.xml file that describes the contents of the package and how catkin should interact with it.
* Creating new package, my_awesome_code which depends on rospy:
```
cd ~/catkin_ws/src
catkin_create_pkg my_awesome_code rospy
```
* The catkin_create_pkg command makes a directory with the same name as the new package (my_awesome_code) with a CMakeLists.txt file, a package.xml file, and a src directory in it. The package.xml file contains a bunch of metadata about your new package. Once you have a created package, you can put your Python nodes in the src directory. Other files go in directories under the package directory. Launch files will go in a directory called launch.

## rosrun
* ROS provides a command-line utility called rosrun that will search a package for the requested program and pass it any parameters supplied on the command line. The syntax is as follows:
```
rosrun package executable [args]
```

## rqt_graph
* *rqt_graph* command can be used to render ROS Qt based graph visualiser.

## roslaunch
* Although rosrun is great for starting single ROS nodes during debugging sessions, most robot systems end up consisting of tens or hundreds of nodes, all running at the same time. Since it wouldn’t be practical to call rosrun on each of these nodes, ROS includes a tool for starting collections of nodes, called roslaunch.
* roslaunch is a command-line tool designed to automate the launching of collections of ROS nodes.
```
roslaunch package launch_file
```
* However, roslaunch operates on launch  les, rather than nodes. Launch files are XML files that describe a collection of nodes along with their topic remappings and parameters.
* roslaunch will automatically instantiate a roscore if one does not exist when roslaunch is invoked.

## Poses, Positions and orientations
* A position is a vector of three numbers (x, y, z).
* An orientation is a vector of three numbers (roll, pitch, yaw).
* Taken together, a (position, orientation) pair is called a pose.
* TF: Any node can be the authority that publishes the current information for some transform(s), and any node can subscribe to transform data, gathering from all the various authorities a complete picture of the robot. This system is implemented in the tf (short for transform) package.

## Topics
* Nodes communicate with each other, exchanging information and data. The most common way to do that is through topics.
*  A topic is a name for a stream of messages with a defined type. For example, the data from a laser range-finder might be sent on a topic called scan, with a message type of LaserScan, while the data from a camera might be sent over a topic called image, with a message type of Image.
* Topics implement a publish/subscribe communication mechanism, one of the more common ways to exchange data in a distributed system.
* Before nodes start to transmit data over topics, they must first announce, or advertise, both the topic name and the types of messages that are going to be sent. Then they can start to send, or publish, the actual data on the topic. Nodes that want to receive messages on a topic can subscribe to that topic by making a request to roscore. After subscribing, all messages on the topic are delivered to the node that made the request.

## Publishing/Receiving to/from a topic
> These can be followed from the C++ ros tutorials.

* Remember to add the dependencies to the package.xml file after writing your nodes
* Once the roscore is running, you can see the list of topics by calling *rostopic list* command.

## Defining a custom message
* ROS messages are defined by special message-definition files in the msg directory of a package.
* These files are then compiled into language specific implementations that can be used in the code.
* The file is in the msg directory of the basics package and you need to catkin_make after the declaration
* Package.xml file needs to know that new message definition have to compiled.
* Similar changes need to be done to the CMakeLists.txt file too
* *rosmsg* list will show all the messages available in that ros package. For instance rosmsg package package_name

## Services
* Services are just synchronous remote procedure calls; they allow one node to call a function that executes in another node.
* The server (which provides the service) specifies a callback to deal with the service request, and advertises the service. The client (which calls the service) then accesses this service through a local proxy.
* The file holding this definition of the service is called *name of service.srv* and is traditionally in a directory called srv in the main package directory (although this is not strictly required).
* Run catkin_make to create the code and class definitions that we will actually use when interacting with the service.
* Make an addition to the package.xml file to reflect the dependencies on both rospy and the message system.
* Tell catkin which service definitions files we want compiled.
* Running catkin_make will generate 3 different classes.
* You can verify the service call definition is what you wanted by running the *rossrv* command.
* Services are a callback-based mechanism. The service provider specifies a callback that will be run when the service call is made, and then waits for requests to come in.
* The simplest way to use a service is to call it using the rosservice command. For our word-counting service, the call looks like this:
```
rosservice call-subcommand service-name arguments
rosservice call word_count 'one two three'
```
* You should use services for things that you only need to do occasionally, or when you need a synchronous reply. The computations in a service callback should take a short, bounded amount of time to complete. If they’re going to take a long time, or the time is going to be highly variable, you should think about using an action.

## Action
*
