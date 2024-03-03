# ROS 2 Tutorial Notes (Windows)
The original documentation is available [here](https://docs.ros.org/en/foxy/index.html).

## Installation
Follow the instructions of [ROS 2 Documentation: Installation](https://docs.ros.org/en/foxy/Installation/Windows-Install-Binary.html).

## CLI tools

### Configuring environment
#### 1. Source setup files.
```
call C:\dev\ros2_foxy\local_setup.bat
```
**Note:** the `ros2_foxy` folder name might differ in your case.

---

#### 2. Auto-sourcing can be added to PowerShell (if using PowerShell instead of Command Prompt)
Go to path `Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1` (if the folder/file does not exist, create it) and write
`Unblock-File C:\dev\ros2_foxy\local_setup.ps1` inside it.

**Note**: the ExecutionPolicy needs to be RemoteSigned or Unrestricted, which can be set
`Set-ExecutionPolicy RemoteSigned`
(if it is only set in PowerShell, then it will NOT be set in PowerShell (x86), vice versa)

---

#### 3. Check environment variables
```
set | findstr -i ROS
```

You should find these variables set:
```
ROS_VERSION=2
ROS_PYTHON_VERSION=3
ROS_DISTRO=foxy
```

---

#### 4. Set ROS_DOMAIN_ID variable
```
set ROS_DOMAIN_ID=<your_domain_id>
```
Or to make it permanent between shell sessions:
```
setx ROS_DOMAIN_ID=<your_domain_id>
```
In short, the domain ID should be a number between 0-101 (inclusive), for more details read [this article](https://docs.ros.org/en/foxy/Concepts/About-Domain-ID.html)

---

#### 5. Set ROS_LOCALHOST_ONLY variable
Set this variable only, if you want to limit ROS 2 communication to localhost.
```
set ROS_LOCALHOST_ONLY=1
```
Or to make it permanent between shell sessions:
```
setx ROS_LOCALHOST_ONLY=1
```

---

### Using turtlesim, ros2 and rqt

#### 1. Install turtlesim
On Windows ROS 2, turtlesim should be installed by default. To check if it is installed:
```
ros2 pkg executables turtlesim
```
The command should return a list of turtlesim executables
```
turtlesim draw_square
turtlesim mimic
turtlesim turtle_teleop_key
turtlesim turtlesim_node
```

---

#### 2. Start turtlesim
```
ros2 run turtlesim turtlesim_node
```

**Note:** due to an unresolved bug, turtlesim might fail to start with the following message:
```
qt.qpa.plugin: Could not find the Qt platform plugin "windows" in ""
```
**Solution:** set environment variable `QT_QPA_PLATFORM_PLUGIN_PATH` with value `C:\dev\ros2_foxy\bin\platforms`. Restart the shell and try to run turtlesim again. (Solution found [here](https://answers.ros.org/question/354707/qtqpaplugin-could-not-find-the-qt-platform-plugin-windows-in/))

#### 3. Use turtlesim
Run a new node to control the turtle in the first node:
```
ros2 run turtlesim turtle_teleop_key
```

---

#### 4. Install rqt
On Windows ROS 2, rqt should be installed by default. To run it:
```
rqt
```

---

#### 5. Use rqt
On first run go to `Plugins > Services > Service Caller`. Use the refresh button to the left of the `Service` dropdown list to ensure all the services of your turtlesim node are available.

---

#### Closing turtlesim
To stop the simulation, enter `Ctrl + C` in the `turtlesim_node` terminal, and `q` in the `turtle_teleopkey` terminal.

---

### Understanding nodes

From now on, I will only mention certain commands, and write less explanation that can be found in the documentation anyways. I will mostly write the necessary commands and some notes that are not mentioned in the documentation.

---

#### ros2 run
The command ros2 run launches an executable from a package.
```
ros2 run <package_name> <executable_name>
```
Example:
```
ros2 run turtlesim turtlesim_node
```

---

#### ros2 node list
This command will show the names of all running nodes.
```
ros2 node list
```

---

#### Remapping
Remapping allows you to reassign default node properties, like node name, topic names, service names, etc., to custom values.

For example reassign the name of a basic `/turtlesim` node:
```
ros2 run turtlesim turtlesim_node --ros-args --remap __node:=my_turtle
```

---

#### ros2 node info
Access more information about a known node:
```
ros2 node info <node_name>
```
Example:
```
ros2 node info /my_turtle
```

---

### Understanding topics
Topics are a vital element of the ROS graph that act as a bus for nodes to exchange messages.<br>
<img src="https://docs.ros.org/en/foxy/_images/Topic-SinglePublisherandSingleSubscriber.gif" alt="topic visualization" width="600"/><br>
A node may publish data to any number of topics and simultaneously have subscriptions to any number of topics.<br>
<img src="https://docs.ros.org/en/foxy/_images/Topic-MultiplePublisherandMultipleSubscriber.gif" alt="topic visualization multiple instances" width="600"/><br>

---

#### rqt_graph
We can use rqt_graph to visualize the changing nodes and topics, as well as the connections between them.
To run rqt_graph, open a new terminal and enter the command:
```
rqt_graph
```
You can also open rqt_graph by opening `rqt` and selecting `Plugins > Introspection > Node Graph`.

---

#### ros2 topic list
Running the `ros2 topic list` command in a new terminal will return a list of all the topics currently active in the system.
To see the types of topics, use `ros2 topic list -t`

---

#### ros2 topic echo
To see the data being published on a topic, use:
```
ros2 topic echo <topic_name>
```

---

#### ros2 topic info
To look at the number of publishers and subsribers, use:
```
ros2 topic info <topic_name>
```

---

#### ros2 interface show
Shows the details of a message type:
```
ros2 interface show <msg type>
```

---

#### ros2 topic pub
If the message structure is known, you can publish data onto a topic directly from command line:
```
ros2 topic pub <topic_name> <msg_type> '<args>'
```
**Note:** argument needs to be in YAML syntax.

Example:
```
ros2 topic pub --once /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"
```
**Note:** `--once` is an optional argument meaning “publish one message then exit”.

Example 2:
```
ros2 topic pub --rate 1 /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"
```
**Note:** `--rate 1` is an optional argument meaning “publish the command in a steady stream at 1 Hz”.

---

#### ros2 topic hz
You can view the rate at which data is published using:
```
ros2 topic hz /turtle1/pose
```

---

### Understanding services
Services are another method of communication for nodes in the ROS graph. Services are based on a call-and-response model versus the publisher-subscriber model of topics. While topics allow nodes to subscribe to data streams and get continual updates, services only provide data when they are specifically called by a client.<br>
<img src="https://docs.ros.org/en/foxy/_images/Service-SingleServiceClient.gif" alt="service visualization" width="600"/><br>
<img src="https://docs.ros.org/en/foxy/_images/Service-MultipleServiceClient.gif" alt="service visualization multiple clients" width="600"/><br>

---

#### ros2 service list
Running the `ros2 service list` command in a new terminal will return a list of all the services currently active in the system.
To see the types of services, use `ros2 service list -t`

---

#### ros2 service type
Services have types that describe how the request and response data of a service is structured. Service types are defined similarly to topic types, except service types have two parts: one message for the request and another for the response.
```
ros2 service type <service_name>
```

---

#### ros2 service find
To find all the services of a specific type, you can use:
```
ros2 service find <type_name>
```

---

#### ros2 interface show
You can call services from the command line, but first you need to know the structure of the input arguments.
```
ros2 interface show <type_name>.srv
```
Example:
```
ros2 interface show turtlesim/srv/Spawn
```
The above command should return this:
```
float32 x
float32 y
float32 theta
string name # Optional.  A unique name will be created and returned if this is empty
---
string name
```
The --- separates the request structure (above) from the response structure (below).

---

#### ros2 service call
You can call a service from command line using:
```
ros2 service call <service_name> <service_type> <arguments>
```
**Note:** `<arguments>` part is optional.

Example:
```
ros2 service call /spawn turtlesim/srv/Spawn "{x: 2, y: 2, theta: 0.2, name: ''}"
```
The output should be the following:
```
requester: making request: turtlesim.srv.Spawn_Request(x=2.0, y=2.0, theta=0.2, name='')

response:
turtlesim.srv.Spawn_Response(name='turtle2')
```

---

### Understanding parameters

#### ros2 param list
To see the parameters belonging to your nodes, use:
```
ros2 param list
```
**Note:** Every node has the parameter `use_sim_time`; it is not unique to turtlesim.

---

#### ros2 param get
To display the type and current value of a parameter, use the command:
```
ros2 param get <node_name> <parameter_name>
```
Example:
```
ros2 param get /turtlesim background_g
```
Expected output:
```
Integer value is: 86
```

---

#### ros2 param set
To change a parameter’s value at runtime, use the command:
```
ros2 param set <node_name> <parameter_name> <value>
```

---

#### ros2 param dump
You can save all of a node’s current parameter values into a file to save them for later by using the command:
```
ros2 param dump <node_name>
```
**Note:** it will automatically save the file in the current working directory your shell is running in.
Dumping parameters comes in handy if you want to reload the node with the same parameters in the future.

---

#### ros2 param load
You can load parameters from a file to a currently running node using the command:
```
ros2 param load <node_name> <parameter_file>
```

---

#### Load parameter file on node startup
To start the same node using your saved parameter values, use:
```
ros2 run <package_name> <executable_name> --ros-args --params-file <file_name>
```
Example:
```
ros2 run turtlesim turtlesim_node --ros-args --params-file ./turtlesim.yaml
```

---

### Understanding actions
Actions are one of the communication types in ROS 2 and are intended for long running tasks. They consist of three parts: a goal, feedback, and a result. Actions are built on topics and services, but actions are preemptable (you can cancel them while executing), and they also provide a steady feedback. They use a client-server model (similar to publisher-subscriber). An “action client” node sends a goal to an “action server” node that acknowledges the goal and returns a stream of feedback and a result.<br>
<img src="https://docs.ros.org/en/foxy/_images/Action-SingleActionClient.gif" alt="action visualization" width="600"/><br>
Not only can the client-side stop a goal, but the server-side can as well. When the server-side chooses to stop processing a goal, it is said to “abort” the goal.

---

#### ros2 action list
To identify all the actions in the ROS graph, run the command:
```
ros2 action list
```
To list the actions with their types indicated, you can use `ros2 action list -t`, just like with topics and services.

---

#### ros2 action info
To further introspect an action, use:
```
ros2 action info <action_name>
```
Example:
```
ros2 action info /turtle1/rotate_absolute
```
Which should return:
```
Action: /turtle1/rotate_absolute
Action clients: 1
    /teleop_turtle
Action servers: 1
    /turtlesim
```

---

#### ros2 interface show
If you need the structure of an action type, use:
```
ros2 interface show <action_type>
```
Example:
```
ros2 interface show turtlesim/action/RotateAbsolute
```
Which will return:
```
# The desired heading in radians
float32 theta
---
# The angular displacement in radians to the starting position
float32 delta
---
# The remaining rotation in radians
float32 remaining
```
The section of this message above the first --- is the structure (data type and name) of the goal request. The next section is the structure of the result. The last section is the structure of the feedback.

---

#### ros2 action send_goal
Send an action goal from the command line with the following syntax:
```
ros2 action send_goal <action_name> <action_type> <values>
```
**Note:** `<values>` need to be in YAML format.

**Note:** Add `--feedback` to see the feedback of the goal. (e.g. `ros2 action send_goal <action_name> <action_type> <values> --feedback`)

Example:
```
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.57}"
```

---

### Using rqt_console to view logs
`rqt_console` is a GUI tool used to introspect log messages in ROS 2. Typically, log messages show up in your terminal. With `rqt_console`, you can collect those messages over time, view them closely and in a more organized manner, filter them, save them and even reload the saved files to introspect at a different time.

---

#### Setup
Start rqt_console in a new terminal with the following command:
```
ros2 run rqt_console rqt_console
```

 - The **first** section of the console is where **log messages** from your system will **display**.
 - In the **middle** you have the option to **filter messages** by excluding severity levels. You can also add more exclusion filters using the plus-sign button to the right.
 - The **bottom**section is for **highlighting messages** that include a string you input. You can add more filters to this section as well.

---

#### Logger levels
 - `Fatal` messages indicate the system is going to terminate to try to protect itself from detriment.
 - `Error` messages indicate significant issues that won’t necessarily damage the system, but are preventing it from functioning properly.
 - `Warn` messages indicate unexpected activity or non-ideal results that might represent a deeper issue, but don’t harm functionality outright.
 - `Info` messages indicate event and status updates that serve as a visual verification that the system is running as expected.
 - `Debug` messages detail the entire step-by-step process of the system execution.

The default level is `Info`. This means that the less severe messages are hidden (in `Info`'s case: `Debug`), and the messages with the default severity level and more-severe levels will be shown (in this case: `Info`, `Warn`, `Error`, `Fatal`).

---

#### Set the default logger level
You can set the default logger level when you first run the /turtlesim node using remapping:
```
ros2 run turtlesim turtlesim_node --ros-args --log-level WARN
```

---

### Launching nodes
Launch files allow you to start up and configure a number of executables containing ROS 2 nodes simultaneously. Running a single launch file with the ros2 launch command will start up your entire system - all nodes and their configurations - at once.

---

#### Running a Launch File
```
ros2 launch <package_name> <launch_file_name>
```
Example:
```
ros2 launch turtlesim multisim.launch.py
```
The above example runs the following launch file:
```
# turtlesim/launch/multisim.launch.py

from launch import LaunchDescription
import launch_ros.actions

def generate_launch_description():
    return LaunchDescription([
        launch_ros.actions.Node(
            namespace= "turtlesim1", package='turtlesim', executable='turtlesim_node', output='screen'),
        launch_ros.actions.Node(
            namespace= "turtlesim2", package='turtlesim', executable='turtlesim_node', output='screen'),
    ])
```

**Note:** The launch file above is written in Python, but you can also use XML and YAML to create launch files. Examples for each can be found [here](https://docs.ros.org/en/foxy/How-To-Guides/Launch-file-different-formats.html).

---

### Recording and playing back data
`ros2` bag is a command line tool for recording data published on topics in your system. It accumulates the data passed on any number of topics and saves it in a database. You can then replay the data to reproduce the results of your tests and experiments.

---

#### 1. Create a directory to store your data in
Example:
```
mkdir bag_files
cd bag_files
```

---

#### 2. Choose a topic
`ros2 bag` can only record data from published messages in topics.

To read more about topics' published data, read about `ros2 topic list`, and `ros2 topic echo <topic_name>`.

---
#### 3. ros2 bag record
To record the data published to a topic, use:
```
ros2 bag record <topic_name>
```
**Note:** Before running this command on your chosen topic, open a new terminal and move into the directory you want to store the recorded data in, because the rosbag file will save in the directory where you run it.

**Note:** Press `Crl+C` to stop recording.

Example:
```
ros2 bag record /turtle1/cmd_vel
```
Output should be something like:
```
[INFO] [rosbag2_storage]: Opened database 'rosbag2_2019_10_11-05_18_45'.
[INFO] [rosbag2_transport]: Listening for topics...
[INFO] [rosbag2_transport]: Subscribed to topic '/turtle1/cmd_vel'
[INFO] [rosbag2_transport]: All requested topics are subscribed. Stopping discovery...
```

---

#### 4. Recording multiple topics
To record more than one topic at a time, simply list each topic separated by a space.
**Note:** The `-o` option allows you to choose a unique name for your bag file.
```
ros2 bag record -o <desired_bag_file_name> <topic_1_name> <topic_2_name> ...
```

Example:
```
ros2 bag record -o subset /turtle1/cmd_vel /turtle1/pose
```

---

#### ros2 bag info
You can see details about your recording by running:
```
ros2 bag info <bag_file_name>
```

Example:
```
ros2 bag info subset
```
Output:
```
Files:             subset.db3
Bag size:          228.5 KiB
Storage id:        sqlite3
Duration:          48.47s
Start:             Oct 11 2019 06:09:09.12 (1570799349.12)
End                Oct 11 2019 06:09:57.60 (1570799397.60)
Messages:          3013
Topic information: Topic: /turtle1/cmd_vel | Type: geometry_msgs/msg/Twist | Count: 9 | Serialization Format: cdr
                 Topic: /turtle1/pose | Type: turtlesim/msg/Pose | Count: 3004 | Serialization Format: cdr
```

---

#### ros2 bag play
**Note:** in turtlesim's case, teleop needs to be stopped (press `Ctrl+C` in the terminal where teleop is running) before replay could be started.

Replay the data stored in the bag file, using:
```
ros2 bag play <bag_file_name>
```

Example:
```
ros2 bag play subset
```
Output:
```
[INFO] [rosbag2_storage]: Opened database 'subset'.
```

---

## Client libraries

### Using `colcon` to build packages

---

#### Install colcon
```
pip install -U colcon-common-extensions
```

#### 1. Create a workspace
Example:
```
md \dev\ros2_ws\src
cd \dev\ros2_ws
```

#### 2. Add some sources
For example we can clone the [examples](https://github.com/ros2/examples) repository into the `src` directory of the workspace:
```
git clone https://github.com/ros2/examples src/examples -b foxy
```

#### 3. Source an underlay
We must source the environment for an existing ROS 2 installation that will provide our workspace with the necessary build dependencies for the example packages, which is achieved by sourcing the stup script provided by a binary installation. This environment is called an **underlay**. Our workspace (e.g. `ros2_ws`) will be an overlay on top of the existing ROS 2 installation.

#### 4. Build the workspace
**Note:** building must be run from a Visual Studio Command Prompt (`x64 Native Tools Command Prompt for VS 2019`) running as Administrator. (This is NOT a terminal in VSCode, this is a separate application).

**Note:** you must be in the **root** of the **workspace**, and **source ROS2**.

In the root of the workspace, run `colcon build`
```
colcon build --symlink-install --merge-install
```
**Note:** Windows doesn’t allow long paths, so `--merge-install` will combine all the paths into the `install` directory.

**Note:** `--symlink-install` allows the installed files to be changed by changing the files in the source space (e.g. Python files or other non-compiled resources) for faster iteration.

**Note:**
```
WNDPROC return value cannot be converted to LRESULT
TypeError: WPARAM is simple, so must be an int object (got NoneType)
```
The above error is supposedly not a problem, can be avoided with `--event-handlers desktop_notification-` argument.

#### 5. Source the environment
```
call install\setup.bat
```

#### 6. Run tests
```
colcon test --merge-install
```

#### Create your own package
Read more about packages [here](https://docs.ros.org/en/foxy/Tutorials/Beginner-Client-Libraries/Colcon-Tutorial.html).

---

### Creating a workspace
A workspace is a directory containing ROS 2 packages.
**Note:** Remember to use a `x64 Native Tools Command Prompt for VS 2019` for executing the following commands, as we are going to build a workspace.
In this part you will put a workspace together using existing packages. You can read below about building your own packages.

#### 1. Source ros2 environment
#### 2. Create new directory
Example:
```
md \ros2_ws\src
cd \ros2_ws\src
```

#### 3. Clone a sample repo
```
git clone https://github.com/ros/ros_tutorials.git -b foxy-devel
```

#### 4. Build the workspace with colcon
```
colcon build --merge-install
```
-******************************************************************-

**Note:** I get a CMAKE error here which is not solved yet.

I am not sure if it is a pathing issue, a Qt issue, or simply

the example packages are deprecated. Will come back

to this problem once I learned package creation.

**Update:** When creating a simple Hello World type executable

in a new package, and building that, the CMAKE error is NOT

appearing, the build finishes successfully.

-******************************************************************-

---

### Creating a package
A package is an organizational unit for your ROS 2 code. Package creation in ROS 2 uses ament as its build system and colcon as its build tool. You can create a package using either CMake or Python, which are officially supported, though other build types do exist.

---

#### What makes up a ROS 2 package (Python)?
 - `package.xml` file containing meta information about the package
 - `resource/<package_name>` marker file for the package
 - `setup.cfg` is required when a package has executables, so `ros2 run` can find them
 - `setup.py` containing instructions for how to install the package
 - `<package_name>` - a directory with the same name as your package, used by ROS 2 tools to find your package, contains `__init__.py`

Example file structure of the simplest possible package:
```
my_package/
    package.xml
    resource/my_package
    setup.cfg
    setup.py
    my_package/
```

---

#### Packages in a workspace
A single workspace can contain as many packages as you want, each in their own folder. You can also have packages of different build types in one workspace (CMake, Python, etc.).

**Note:** Best practice is to have a src folder within your workspace, and to create your packages in there. This keeps the top level of the workspace “clean”.

A trivial workspace example:
```
workspace_folder/
    src/
      cpp_package_1/
          CMakeLists.txt
          include/cpp_package_1/
          package.xml
          src/

      py_package_1/
          package.xml
          resource/py_package_1
          setup.cfg
          setup.py
          py_package_1/
      ...
      cpp_package_n/
          CMakeLists.txt
          include/cpp_package_n/
          package.xml
          src/
```

---

#### Create a package
The command syntax for creating a new package in ROS 2 in Python is:
```
ros2 pkg create --build-type ament_python <package_name>
```

Example:
```
ros2 pkg create --build-type ament_python --node-name my_node my_package
```
**Note:**  the optional argument --node-name creates a simple Hello World type executable in the package.

---

#### Build a package
Putting packages in a workspace is especially valuable because you can build many packages at once by running colcon build in the workspace root.
```
colcon build --merge-install
```
To build only a desired package of the workspace, run:
```
colcon build --packages-select <package_name>
```
**Note:** `--merge-install` option is required when using `--package-select` if the directory `install` was created with `--merge-install`.

**Note:** package build finishes, but with stderr outputs (some names are deprecated, due to newer versions (of Python?) switching from `-` to `_`)

Example output for above stderr:
```
Usage of dash-separated 'script-dir' will not be supported in future
versions. Please use the underscore name 'script_dir' instead.

By 2024-Sep-26, you need to update your project and remove deprecated calls
or your builds will no longer be supported.
```

---

#### Source the setup file
```
call install/local_setup.bat
```

---

#### Use the package
```
ros2 run <package_name> <node_name>
```

---

#### Examine package contents
You can find the package contents in your package folder.

e.g. in `ros2_ws/src/my_package`:
```
my_package  package.xml  resource  setup.cfg  setup.py  test
```
**Note:** my_node.py is inside the my_package directory. This is where all your custom Python nodes will go in the future.

---

#### Customize package.xml
**Note:** The `license` and `description` declarations are not automatically set, but are required if you ever want to release your package.

It can be found at `<workspace_folder>/src/<package_name>/package.xml`

What to do in the `package.xml` file?
 - input your name and email on the `maintainer` line if it hasn’t been automatically populated for you.
 - edit the `description` line to summarize the package
 - update the `license` line (you can read more about open source licenses [here](https://opensource.org/license))


The setup.py file contains the same description, maintainer and license fields as package.xml, so you need to set those as well. They need to match exactly in both files. 

The setup.py file can be found at `<workspace_folder>/src/<package_name>/setup.py` (same place the `package.xml` was)

Make sure to update the required fields in both files.

---

### Writing a simple publisher and subscriber
Creating nodes that pass information in the form of string messages to each other over a topic.

---

#### 1. Create a package
Example:
```
ros2 pkg create --build-type ament_python py_pubsub
```

---

#### 2. Write the publisher node
In our example navigate into `ros2_ws/src/py_pubsub/py_pubsub`. Recall that this directory is a Python package with the same name as the ROS 2 package it’s nested in.

Download example talker code:
```
curl -sk https://raw.githubusercontent.com/ros2/examples/foxy/rclpy/topics/minimal_publisher/examples_rclpy_minimal_publisher/publisher_member_function.py -o publisher_member_function.py
```
**Note:** this needs to be in Windows Command Prompt.

#### 2.1 Examine the code
To understand how the talker code works visit [this page](https://docs.ros.org/en/foxy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Py-Publisher-And-Subscriber.html#examine-the-code)

#### 2.2 Add dependencies
Navigate one level back to the parent directory (in the example's case to `ros2_ws/src/py_pubsub`).

Open `package.xml`. Make sure to fill the `<description>`, `<maintainer>` and `<license>` tags.

After these lines, add the dependencies. In our example's case, the following:
```
<exec_depend>rclpy</exec_depend>
<exec_depend>std_msgs</exec_depend>
```
This declares the package needs `rclpy` and `std_msgs` when its code is executed.

#### 2.3 Add an entry point
Open the `setup.py` file. Again, match the `maintainer`, `maintainer_email`, `description` and `license` fields to your `package.xml`:
```
maintainer='YourName',
maintainer_email='you@email.com',
description='Examples of minimal publisher/subscriber using rclpy',
license='Apache License 2.0',
```

Then add the following line within the `console_scripts` brackets of the `entry_points` field:
```
entry_points={
        'console_scripts': [
                'talker = py_pubsub.publisher_member_function:main',
        ],
},
```
After that, the `setup.cfg` file should be correctly populated automatically, like so:
```
[develop]
script-dir=$base/lib/py_pubsub
[install]
install-scripts=$base/lib/py_pubsub
```
This is simply telling setuptools to put your executables in `lib`, because `ros2 run` will look for them there.

---

#### 3 Write the subscriber node
These are almost the same steps, as writing the publisher node, but with a different python code, for more details, read [this](https://docs.ros.org/en/foxy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Py-Publisher-And-Subscriber.html#write-the-subscriber-node)

#### 3.2 Add an entry point
The setup.py file's entry points should look like this:
```
entry_points={
        'console_scripts': [
                'talker = py_pubsub.publisher_member_function:main',
                'listener = py_pubsub.subscriber_member_function:main',
        ],
},
```

---

#### 4. Build and run
In the root of the workspace, build the package. E.g.
```
colcon build --merge-install --packages-select py_pubsub
```
In new terminals source ROS 2 and then source your setup files (from the root directory of the workspace):
```
call install/setup.bat
```
Run the talker node:
```
ros2 run py_pubsub talker
```
In a separate terminal, do the sourcing, as well, and then run the listener node:
```
ros2 run py_pubsub listener
```
You should see the nodes logging what they are sending/receiving. You can stop the nodes from spinning with `Ctrl+C`.

---

### Writing a simple service and client
When nodes communicate using services, the node that sends a request for data is called the client node, and the one that responds to the request is the service node. The structure of the request and response is determined by a .srv file.

---

#### 1. Create a package
In the `src` folder of the workspace, run:
```
ros2 pkg create --build-type ament_python py_srvcli --dependencies rclpy example_interfaces
```
The `--dependencies` argument will automatically add the necessary dependency lines to `package.xml`. `example_interfaces` is the package that includes the .srv file you will need to structure your requests and responses:
```
int64 a
int64 b
---
int64 sum
```
**Note:** The first 2 lines are the parameters of the request, the one below the dashes is the response.

---

#### 1.1. Update `package.xml`
Because you used the `--dependencies` option during package creation, you don’t have to manually add dependencies to `package.xml`. You must add description, maintainer name and email, and license information, still.

---

#### 1.2 Update `setup.py`
Add the same information as you wrote in the `package.xml`

---

#### 2. Write the service node
Here, for this example, in the `ros2_ws/src/py_srvcli/py_srvcli` directory, create a new file called `service_member_function.py`, and paste the code from [here](https://docs.ros.org/en/foxy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Py-Service-And-Client.html#write-the-service-node), where you can also read about the details of the code.

---

#### 2.1 Add an entry point
Add the following line between the `'console_scripts':` brackets in `setup.py`:
```
'service = py_srvcli.service_member_function:main',
```

---

#### 3. Write the client node
Inside `ros2_ws/src/py_srvcli/py_srvcli` create a file `client_member_function.py` and paste the code from [here](https://docs.ros.org/en/foxy/Tutorials/Beginner-Client-Libraries/Writing-A-Simple-Py-Service-And-Client.html#write-the-client-node), where you can also read about the details of the code.

---

#### 3.1 Add an entry point
The `entry_points` in the `setup.py` should look like this:
```
entry_points={
    'console_scripts': [
        'service = py_srvcli.service_member_function:main',
        'client = py_srvcli.client_member_function:main',
    ],
},
```

---

#### 4 Build and run
Build the package, source the setup files and run the nodes. For more details about these actions, see the above lines of this file.

---

### Creating custom msg and srv files
For this tutorial you will be creating custom .msg and .srv files in their own package, and then utilizing them in a separate package. Both packages should be in the same workspace.

---
#### 1. Create a new package

We will use the pub/sub and service/client packages created above, so make a new package in the same workspace:
```
ros2 pkg create --build-type ament_cmake tutorial_interfaces
```
**Note:** it is, and can only be, a CMake package, but this doesn’t restrict in which type of packages you can use your messages and services. You can create your own custom interfaces in a CMake package, and then use it in a C++ or Python node, which will be covered in the last section.

The .msg and .srv files are required to be placed in directories called msg and srv respectively. Create the directories in ros2_ws/src/tutorial_interfaces:
```
mkdir msg srv
```

---

#### 2. Create custom definitions

#### 2.1 msg definition
In `tutorial_interfaces/msg` create a file `Num.msg` with the following content, declaring its data structure:
```
int64 num
```

Also in `tutorial_interfaces/msg` create a file `Sphere.msg` with the following content:
```
geometry_msgs/Point center
float64 radius
```

#### 2.2 srv definition
In the `tutorial_interfaces/srv` directory, make a new file `AddThreeInts.srv` with the following request and response structure:
```
int64 a
int64 b
int64 c
---
int64 sum
```
This is your custom service that requests three integers named a, b, and c, and responds with an integer called sum.

---

#### 3 Conversion
To convert the interfaces you defined into language-specific code (like C++ and Python) so that they can be used in those languages, add the following lines to `CMakeLists.txt`:
```
find_package(geometry_msgs REQUIRED)
find_package(rosidl_default_generators REQUIRED)

rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/Num.msg"
  "msg/Sphere.msg"
  "srv/AddThreeInts.srv"
  DEPENDENCIES geometry_msgs # Add packages that above messages depend on, in this case geometry_msgs for Sphere.msg
)
```
**Note:** This should be added in the file before the `ament_package()` line.

---

#### 4. Package
Because the interfaces rely on `rosidl_default_generators` for generating language-specific code, you need to declare a build tool dependency on it. `rosidl_default_runtime` is a runtime or execution-stage dependency, needed to be able to use the interfaces later. The `rosidl_interface_packages` is the name of the dependency group that your package, `tutorial_interfaces`, should be associated with, declared using the `<member_of_group>` tag.

Add the following lines within the `<package>` element of `package.xml`:
```
<depend>geometry_msgs</depend>
<buildtool_depend>rosidl_default_generators</buildtool_depend>
<exec_depend>rosidl_default_runtime</exec_depend>
<member_of_group>rosidl_interface_packages</member_of_group>
```

---

#### 5. Build the `tutorial_interfaces` package
```
colcon build --merge-install --packages-select tutorial_interfaces
```

-***********************************************************************************-

**Note:**
I got `ModuleNotFoundError: No module named 'catkin_pkg'` error.

**OLD Solution:** In this case, check the python path with `echo %PYTHONPATH%`. For me it was  `C:\dev\ros2_foxy\Lib\site-packages`, but pip installed catkin to ` C:\python38\Lib\site-packages`. To append the latter path to `%PYTHONPATH%`, use this command `set PYTHONPATH=%PYTHONPATH%;C:\python38\Lib\site-packages`. This resolved the issue for me.

**Update:** It, in fact, did not resolve the issue. `catkin_pkg` is found, but it (most likely CMake) tries to use a python executable at `C:\Users\youruser\AppData\Local\Microsoft\WindowsApps\python3.exe` instead of `C:\Python38\python.exe`. Tried modifying the CMakeList.txt with `set(PYTHON_EXECUTABLE "C:/Python38/python.exe")` but it still fails to build, but does not show any errors, but only warnings.

**FINAL SOLUTION:** 
Add the following to the beginning of the `CMakeLists.txt` of your package (after the CMake and project declarations):
```
set(PYTHON_EXECUTABLE "C:/Python38/python.exe")
find_package(Python3 COMPONENTS Interpreter Development)
```
It sets the correct path of Python for CMake, and THEN AFTER it was set, tries to find Python, and then runs the other stuff. This worked and the package build finished successfully.

-***********************************************************************************-

---

#### 6. Confirm msg and srv creation
Source:
```
call install/setup.bat
```

```
ros2 interface show tutorial_interfaces/msg/Num
```
Should return:
```
int64 num
```
And
```
ros2 interface show tutorial_interfaces/msg/Sphere
```
Should return:
```
geometry_msgs/Point center
        float64 x
        float64 y
        float64 z
float64 radius
```

And
```
ros2 interface show tutorial_interfaces/srv/AddThreeInts
```
Should return:
```
int64 a
int64 b
int64 c
---
int64 sum
```

---

#### 7. Test the new interfaces
#### 7.1 Testing `num.msg` with pub/sub
Optionally make a new pubsub package, or just modify the previous one, based on [these](https://docs.ros.org/en/foxy/Tutorials/Beginner-Client-Libraries/Custom-ROS2-Interfaces.html#test-the-new-interfaces).