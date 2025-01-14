# Development 2021

This is an archive of the `development` channel of the [Webots Discord server](https://discordapp.com/invite/nTWbN9m) for year 2021.

## January

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/05/2021 09:27:08
The conversation in technical-questions made me think: Wouldn't it be a nice feature, to have an 'odometry' device? Which retrieves the pose of it's parent. Of course this can be done manually with a supervisor, however that is more complicated for beginners and well, requires a supervisor.

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/05/2021 09:47:22
Yes, but I believe that such an odometry device doesn't exists in real life...

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/05/2021 09:56:13
no it doesnt, but it can be a good first step / validation tool. This is used all the time in ROS and Gazebo


It could be implemented in the Robot node perhaps. "getPosition", "getOrientation"

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/05/2021 10:01:28
It is already there when you turn your robot into a supervisor.

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/05/2021 10:10:39
well I guess that's true. Still could be useful to have a device one could add to Solids/Transforms, in order to retrieve the pose


Found a strange behavior, exporting a robot as URDF uses the boundingObject as the visual geometry, not the shape. Is this on purpose? Or simply because mesh extraction is not implemented yet?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/07/2021 08:00:15
Yes, it is described in the note under `wb_robot_get_urdf` documentation. We have a basic implementation of URDF exporter that can work for most use-cases (publish ROS transforms, calculate inverse kinematics, and similar), but only a basic visualization is possible.

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/07/2021 10:01:47
`@Darko Lukić` launching the dynamic rviz with ros2, this Error comes up
%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/796679973011718164/unknown.png)
%end



%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/796680042905337867/unknown.png)
%end


this is part of the console output
%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/796680283385888779/unknown.png)
%end


I suspect that the reason is rviz launching before webots and ros2 is fully loaded


loading the same config again once it is running, no errors come up

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/07/2021 10:04:06
RViz2 is a bit buggy, just uncheck/check the RobotModel checkbox and it should work

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/07/2021 10:04:29
you're right


is there a quick and easy way to include the world frame in the tf broadcast?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/07/2021 10:06:26
You can add a static transform publisher to your launch file:

```py
static_tf = Node(
  package='tf2_ros',
  executable='static_transform_publisher',
  name='static_transform_publisher',
  output='log',
  arguments=['0.0', '0.0', '0.0', '0.0', '0.0', '0.0', 'world', 'base_link']
)
```

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/07/2021 10:07:00
what if the robot is mobile?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/07/2021 10:07:48
Than you have to publish the transforms manually :/

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/07/2021 10:09:00
does that work alongside the robot\_state\_publisher?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/07/2021 10:10:07
Sure

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/07/2021 10:10:37
maybe that should be a feature of the webots\_ros2\_core

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/07/2021 10:11:42
We do it here for example:

[https://github.com/cyberbotics/webots\_ros2/blob/7aa84877e0dc5d668e923fd2f23ebd4acb865d90/webots\_ros2\_examples/webots\_ros2\_examples/khepera\_driver.py#L92-L105](https://github.com/cyberbotics/webots_ros2/blob/7aa84877e0dc5d668e923fd2f23ebd4acb865d90/webots_ros2_examples/webots_ros2_examples/khepera_driver.py#L92-L105)


The robot state publisher is in the core, what would you like to add in addition to it?

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/07/2021 10:13:18
have an option (or by default) to pulbish the world - baselink transform


static robots are one thing, but especially for mobile ones, it is kinda essential.

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/07/2021 10:14:52
But that is something that a user has to handle

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/07/2021 10:15:37
Gazebo publishes it by default, people are used to simply selecting "world" in rviz

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/07/2021 10:17:16
Gazebo publishes transform between `world` and `base_link` for mobile robots? That sounds like cheating


In the real-world transform between `world` and `base_link` is not available

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/07/2021 10:18:02
maybe it is just static. Has been a while, dont remember

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/07/2021 10:18:31
We can easily add it using Supervisor, but it is strange to have something like that

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/07/2021 10:20:13
hmmm where exactly would I add that tf\_static node?


for example when using this: [https://github.com/cyberbotics/webots\_ros2/blob/master/webots\_ros2\_universal\_robot/launch/universal\_robot\_rviz\_dynamic.launch.py](https://github.com/cyberbotics/webots_ros2/blob/master/webots_ros2_universal_robot/launch/universal_robot_rviz_dynamic.launch.py)


line 43?


jup, works 🙂

##### Moha 01/13/2021 07:53:46
hi guys

is it possible to use "vscode" as my IDE?

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/13/2021 07:55:10
Sure.

##### Ragemor 01/13/2021 08:06:20
[https://cyberbotics.com/doc/guide/using-your-ide](https://cyberbotics.com/doc/guide/using-your-ide)

##### Moha 01/13/2021 08:07:09
👍

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/13/2021 08:44:39
I'm curious, wouldn't it be possible to set system environment variables during installation?


`@Darko Lukić` `@Olivier Michel` I've got moveit2 working with ros2 and webots. I also made some progress of creating an easy to use template or automatic setup for robotic arms


I think one important feature would be, to automatically extract the urdf from webots, including meshes.


I already did some work in that direction with my proto-splitter script. That one worked, extracting .obj or vrml meshes, but without smoothing

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/13/2021 09:42:21
Probably yes on Windows, probably not possible on Mac and Linux...

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/13/2021 09:44:28
ahh okay


Also I was wondering: could it be possible to launch a ROS1 or ROS2 launch file from within webots?

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/13/2021 11:23:51
I am not sure this is a good idea as we want to be able to interact with ROS in the console...

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/13/2021 15:44:30
It may be possible to implement events in the launch file (like here [https://github.com/cyberbotics/webots\_ros2/blob/38d88e01fe174a8a00731f554f1a8646b9127bd2/webots\_ros2\_demos/launch/armed\_robots.launch.py#L63](https://github.com/cyberbotics/webots_ros2/blob/38d88e01fe174a8a00731f554f1a8646b9127bd2/webots_ros2_demos/launch/armed_robots.launch.py#L63)), so if reset controllers in Webots it supposed to restart certain ROS2 nodes as well within the launch file. This would be useful feature and it would speed up development significantly (as you don't need to reload the world).

##### babaev1 01/16/2021 20:00:53
Dear Sirs, recently Organizing Commitee of Humanoid League of Robocup Federation decided to host Summer World Championship in virtual format with organizing games between teams within simulator. Currently simulator platforms are under consideration for choosing. There are going to be games between 2 teams , with each team providing 4 humanoid robots  equipped by 23-25DoF servomotors, 1-2 imus, 1-2 cameras. Imortant point is streaming of images from cameras to external controller softwares of teams. Currently built in webots camera streams images in non-compressed format. Supposing simultaneously streaming images from 8 cameras with resolution 640x480 RGB  with 30 fps simulation is expected to be dramatically slowing down.  Some of teams are expected to use stereo cameras, means number of cameras in this case will be doubled.


Do you plan to add compression to image streaming in near future, or there are some implementations which I am not familiar with?

##### cnbarcelo 01/17/2021 15:43:05
Hi! Where can I find the sensors visualization code? I'm mainly looking for the the lidar visualization.

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/17/2021 17:59:21
`@cnbarcelo` You can use `View / Optional Rendering / Show Lidar Point Cloud` and `View / Optional Rendering / Show Lidar Rays Paths`:

[https://cyberbotics.com/doc/guide/the-user-interface](https://cyberbotics.com/doc/guide/the-user-interface)



Remember, to show the point cloud, you need to enable the point cloud first. You can do it from the robot window (double click on the robot) or from your controller:

[https://cyberbotics.com/doc/reference/lidar#wb\_lidar\_enable\_point\_cloud](https://cyberbotics.com/doc/reference/lidar#wb_lidar_enable_point_cloud)

##### cnbarcelo 01/17/2021 18:03:15
Yeah, I do know that, but I migrated from 2020 to 2021 and it's not working well. I wanted to check if something changed before creating a ticket.


If there's any bug to work on, I might be able to collaborate.

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/17/2021 18:06:12
It works fine for me. Can you tell more details about your issue?
%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/800425762247278592/unknown.png)
%end

##### cnbarcelo 01/17/2021 18:07:38
The visualization blinks. It works for a couple of seconds, stop working, and starts working again, completely randomly.


It happens in my simulation and also in the lidar world example.


I'll record a video and share it here later on today.

##### Chernayaten 01/17/2021 18:08:54
Hey Darko, I also wrote a comment few weeks ago about this in <#565154703139405824> . I run the lidar.wbt example and although it shows the point cloud at first it just stops working correctly


In my simulation it doesn't work at all

##### cnbarcelo 01/17/2021 18:09:43
That's exactly what happens, will share a video later then, so you Darko can see it.


I didn't see your comment in <#565154703139405824> , that's way I came here with the issue.

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/17/2021 18:10:38
It may be specific to GPU or OS. Could you please report the bug:

[https://github.com/cyberbotics/webots/issues/new?assignees=&labels=&template=bug\_report.md](https://github.com/cyberbotics/webots/issues/new?assignees=&labels=&template=bug_report.md)

##### Chernayaten 01/17/2021 18:12:43
What info would you like besides GPU and OS?

##### cnbarcelo 01/17/2021 18:16:21
I'm running Ubuntu and an Nvidia card.

I'm out of office now, will check if you already created the issue when I'm back, and will add the info of my system there, or will create it by my own.

##### Chernayaten 01/17/2021 18:24:03
[https://github.com/cyberbotics/webots/issues/2660](https://github.com/cyberbotics/webots/issues/2660)



I added a short video to show it

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/17/2021 19:23:47
Thank you, we will check it out


It may be related to this PR:

[https://github.com/cyberbotics/webots/pull/2509](https://github.com/cyberbotics/webots/pull/2509)



It is possible that the visualization part fails to handle infinity values.

##### Chernayaten 01/17/2021 20:12:04
After a few tests it seems you are correct. The same issue presents when the robot gets close to objects because " If the range value is smaller than the minRange value then infinity is returned."

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/18/2021 07:58:52
In Webots images are read from the GPU memory directly into a shared memory segment which is readable by both Webots and the local robot controller to optimize efficiency and avoid unnecessary image copy. In the context of RoboCup (and other frameworks, like ROS or ROS2), the local robot controller acts as a relay between the simulator and the actual robot controller which may run on a different machine over the network. Therefore, while implementing this relay controller, it is very easy to add JPEG compression to the camera image to be sent to the actual controller over the network. This is indeed exactly what happens when you use a ROS 2 controller with Webots to publish a camera image, it gets automatically compressed in JPEG (and you can even choose the compression ratio) before being sent to the controller.

##### babaev1 01/18/2021 09:23:07
Thank you for prompt reply. For me it is clear. I shall share your answer with Robocup community for possible definitions.

##### Moha 01/19/2021 09:49:08
hi guys

Is it possible to use spot robot SDK releasing from Boston dynamics in webots ?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/19/2021 10:01:18
Hello `@Moha`, Webots doesn't provide support for Spot SDK. It is not in the scope of Webots, but I advise you to create a discussion:

[https://github.com/cyberbotics/webots/discussions/categories/ideas](https://github.com/cyberbotics/webots/discussions/categories/ideas)

If there is a strong need (many requests), somebody from the community may create the integration.

##### Moha 01/19/2021 10:06:20
hi dear Darko

good idea, ok

but may I ask you how is it possible to request for it ?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/19/2021 10:08:39
You can create a discussion:

[https://github.com/cyberbotics/webots/discussions/new](https://github.com/cyberbotics/webots/discussions/new)



Explain why the integration is necessary, how big impact it will have on the community...

##### Moha 01/19/2021 10:14:43
I've understood what you mean in first reply, but I mean I want to know how other people can show their enthusiasm for it ?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/19/2021 10:15:56
They can join the discussion, like it, suggest new ideas

##### Moha 01/19/2021 10:52:14
ok thank you 👍

##### pk1jk1 01/20/2021 05:59:29
What is the best way to locally edit a webots package?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/20/2021 07:30:40
You can compile as here:

[https://github.com/cyberbotics/webots\_ros2/wiki/Build-and-Install](https://github.com/cyberbotics/webots_ros2/wiki/Build-and-Install)

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/20/2021 09:48:01
Is there documentation for custom robot window? I cannot seem to find it.

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/20/2021 09:58:59
[https://cyberbotics.com/doc/guide/controller-plugin#robot-window](https://cyberbotics.com/doc/guide/controller-plugin#robot-window) (but it's very limited).


[https://cyberbotics.com/doc/guide/controller-plugin?version=master#robot-window](https://cyberbotics.com/doc/guide/controller-plugin?version=master#robot-window) (this is a newer version, with much more details)


(but refers to the master branch, not the released version)


(to be released in the next revision)

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/20/2021 10:01:32
yeah, i'm excited for the new changes there. I'm gonna wait then until I dive back in

##### fowzan 01/21/2021 18:08:36
I would love to simulate spot mini on my terminal , could you share links if available

##### pk1jk1 01/22/2021 16:58:40
Having trouble editing a package locally, please message me if you can help


Do you know why I am getting this error?
%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/802613647226044436/unknown.png)
%end

##### Bitbots\_Jasper [Moderator] 01/23/2021 19:02:50
you seem to be missiong ros-<version>-console-bridge

##### adiagr 01/24/2021 18:32:16
You can first threshold the image and then find contours in the image. Select the contour with the maximum area. Finally, find centroid of that contour and use it as an input to a PID controller or something similar to drive your robot

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/25/2021 15:11:40
I got the suggestion from a user to add voice channels to the discord server.  Could be a nice addition

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/25/2021 15:29:48
I just created one. Can you try it?

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/25/2021 15:30:06
seems to work

##### Krish 01/25/2021 15:33:10
oh wow, already created.

##### pk1jk1 01/25/2021 15:55:58
Does anyone have experience creating an overhead map with a camera? And potentially stitching multiple images together to create an encompassing overhead map?

##### babaev1 01/25/2021 16:04:06
There is good feature in Coppelia which I couldn’t find in Webots: position and orientation of each node can be measured and modified from GUI.

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/25/2021 17:13:51
you can do the same in Webots in the scene tree

##### babaev1 01/25/2021 17:34:59
Yes, it is possible to read absolute position in scene tree, but modification of absolute position or orientation is not accessible there.

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/25/2021 17:38:12
it is


just change the values
%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/803317976853905448/unknown.png)
%end

##### babaev1 01/25/2021 17:43:41
In case if in children of parent Solid child Solid is added direct change of values of child Solid appears complex especially in case if in parent Solid rotation is not zero.


I try to compose humanoid robot with 6 DOF in each branch. Direct modification of absolute position of deep level child elements is really challenging.

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/25/2021 17:50:44
that is the nature of kinematic chains


that is where you need inverse kinematics

##### babaev1 01/25/2021 17:56:54
In case if I select node in scene tree I can see absolute position and rotation of node, but values are not editable. Possibility of modification of absolute position and orientation through editing values could be helpful.


In Coppelia it is possible

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/26/2021 09:11:26
`@babaev1` If I understand you correctly, you want to position a child node using absolute coordinates, which would automatically calculate the relative coordinates to the parent?

##### babaev1 01/26/2021 13:51:17
I am using Coppelia for teaching robotics in school since 2 years. There is functionally which I consider useful and I use it often. It is possible to call pop-up widow for reading and editing absolute coordinate of any node which you select on scene tree or directly on 3D rendering window. After giving new coordinate and position old link to parent node is replaced by new link. Position and orientation of parent node remain unchanged. Position and orientation of all child nodes changes together with selected node new position and orientation. In case if you want to protect complex structures from being destroyed by this action node can be protected from being selected in node properties. I started to study Webots 2 weeks ago and I didn’t find similar useful functionality.


window (not widow)

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/27/2021 10:35:31
`@babaev1` So you want the reference frame selected here (in this case 'Absolute') to change the axis in the 3D view and the translation + rotation field in the scene tree. So they can be edited in the chosen frame. However, the saved definition is still the relative position to its parent.
%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/803936219175452702/unknown.png)
%end


`@Olivier Michel` this could be a nice feature and should not necessarily be too complex to include

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/27/2021 10:41:53
Yes, I believe changing these position and rotation fields from read-only to read/write should be fairly easy to implement. However, I am not sure to understand why it seems useful. In general objects can be moved from the root-level translation/rotation. Moving an object inside its parent is not something we want to do from the global coordinate system. So, I am not sure to understand the use case for this...

##### babaev1 01/27/2021 10:43:34
This is useful during building new robot


In order to build 23 DOF robot from primitives I spent 2 days in Coppelia and about 1 week in Webots.

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/27/2021 10:54:57
Ok, thank you for the explanation. Then, you should probably open an issue about this (or implement it by yourself if you want).

##### babaev1 01/27/2021 11:01:24
First variant is more realistic for my statement of amateur programmer.

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/27/2021 11:09:23
I believe this really not so difficult to implement...

##### babaev1 01/28/2021 22:07:31
Dear Webots developers, do you have plan to add Russian language to variety of GUI languages in 2021? Together with pandemic and moving robotic lessons and competitions to virtual format there is higher interest to use simulation software in education. Unfortunately not many teachers and students are fluent with English in Russian schools.  Adding optional Russian language to GUI can give good advantage to Webots to be widely used in Russian schools.

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/28/2021 22:15:23
Hello `@babaev1`, feel free to add translations, we will be happy to review your PR:

[https://github.com/cyberbotics/webots/tree/master/resources/translations](https://github.com/cyberbotics/webots/tree/master/resources/translations)

##### babaev1 01/28/2021 22:19:39
Thank you. Good to know that it is so easy. I shall arrange people at my side to add Russian translation.

##### Professor Felix 01/29/2021 04:31:27
does webots use git LFS? I'm just a student who cloned the repo to try and understand some of the internal codebase for educational purposes and I noticed the file size was over 5GB

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/29/2021 07:48:43
You should not need git LFS to check out Webots as Webots doesn't contain any file larger than 5GB.

##### Professor Felix 01/29/2021 08:33:24
I know I just thought GitHub itself preferred to keep repositories under 2GB


But I think I heard wrong

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 01/29/2021 08:34:33
Yes, there not such a limit in GitHub.

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/29/2021 09:58:03
`@Darko Lukić` wouldn't it be possible to run a translator api over the language files and do a base translation? That seems like a fairly easy and quick way to at least offer more languages. Of course they may not be perfect, but might be bettter than nothing

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 01/29/2021 10:08:53
I think it is possible, but I am concern about the quality of the translations.

##### Bitbots\_Jasper [Moderator] 01/29/2021 10:10:29
I think the quality would be rather poor since there is often only a small snippet of text and translation algorithms usually work much better with context, it might be worth a shot but a native or very fluent speaker would need to rework it

##### Ginzo1 01/29/2021 12:47:01
Hello, everyone! I've just recently pick up WeBots as I have an university project that requires to be simulated. It is a robot design project for which I will need a hydraulic scissors lift. Since I have a relatively basic knowledge on mechanics and kinematics but I am still really inexperienced with WeBots. Since the scissors lift is a quite common system, I was wondering if there is some information somewhere on how to create one on my own or see how it is supposed to be done from scratch? Thanks in advance!

##### Bitbots\_Jasper [Moderator] 01/29/2021 12:49:47
`@Ginzo1` this question has been asked a while back, maybe there is something useful for you there [https://cyberbotics.com/doc/discord/development-2020#lukulus-11132020-10-48-16](https://cyberbotics.com/doc/discord/development-2020#lukulus-11132020-10-48-16)

##### babaev1 01/30/2021 10:34:05
Dear Webots developers, it is more or less clear how to translate GUI to Russian language. Students in our University started to undertake this work. Separate story is how to translate and publish User Guide and Reference Manual which  is located in website of Cyberbotics. There are 2 variants: 1. Russian translation will be published in Cyberbotics site along with English. 2. In Russian GUI link to User Guide will be different, then we can undertake providing space on web for translated User Guide.

##### Simon Steinmann [ROS 2 Meeting-Moderator] 01/30/2021 14:18:53
`@babaev1` Awesome! Thank you for the contribution. I'm sure the devs will do everything they can to support your effort!


[https://github.com/cyberbotics/webots/tree/master/docs](https://github.com/cyberbotics/webots/tree/master/docs) this is the documentation location of Webots. "guide" and "reference" are the 2 big documentation directories. I'm sure you can make a fork and start translating these. `@Olivier Michel` Perhaps we should slightly change the structure to accommodate any new translations.

##### babaev1 01/31/2021 11:10:23
Thank you, I shall look for opportunity to make this part to be translated too.

## February

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 02/01/2021 10:52:38
`@babaev1`: thank you for undertaking this translation effort.

For translating the GUI, please follow the procedure at [https://github.com/cyberbotics/webots/tree/released/resources/translations](https://github.com/cyberbotics/webots/tree/released/resources/translations)

We will be happy to merge your contribution into Webots.

For translating the manuals, I believe the option 2 is the best for us: we will include in the user guide and reference manual links to community contributed translations.

##### Lumii 02/09/2021 10:36:01
is there any tutorial how i can start writing a program to actually move (UR) my robot in the simulation?

##### Bitbots\_Jasper [Moderator] 02/09/2021 10:37:40
[https://cyberbotics.com/doc/guide/tutorial-4-more-about-controllers](https://cyberbotics.com/doc/guide/tutorial-4-more-about-controllers)


especially the program the controller section


or if you want to do actual motion planning and more complicated motions like planned trajectories or constraints on those trajectories, I would recommend ROS with moveit, but that is quite a project to get into

##### Lumii 02/09/2021 10:45:31
ye i use to work with moveit but moveit2 currently only supports c++ 

and im tryin to create a ROS2 class for my university and it should be in python because the students are learing python in prev classes. So im lookin for an alternativ so that the students can still learn ros2 but with python instead of c++

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/09/2021 11:31:47
`@Lumii` Cyberbotics and I also developed a standalone IKFast Python module


that makes ik super fast and easy to use in webots


[https://github.com/cyberbotics/pyikfast](https://github.com/cyberbotics/pyikfast)


This includes an example I made with the IRB4600 robot. You only have to recompile the pyikfast solver for your robot. Everything else should work out of the box (robot needs to be supervisor, And the init values for this example may have to be changed). I tried my best to comment the code. PM me if you have questions or suggestions
> **Attachment**: [irb4600\_ikfast\_sample\_2.zip](https://cdn.discordapp.com/attachments/565155651395780609/808666959016099840/irb4600_ikfast_sample_2.zip)

##### Lumii 02/09/2021 15:09:09
Oh thanks a lot I’ll look into it!

##### Chernayaten 02/10/2021 22:52:00
Double clicking a .wbt file to start Webots, from a path-file inside a Greek folder crashes the program. To be more specific, Webots starts with the following message showing on the console window for a split second before the program terminates automatically.
%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/809194994279645214/unknown.png)
%end


If I initiate the webots program from its shortcut I can normally load the world without any issues


I'd argue that besides the incorrect handling of the greek characters (and possibly other characters as well), the program shouldn't crash if it fails to load the world

##### Olivier Michel [ROS 2 Meeting-Cyberbotics] 02/11/2021 06:36:27
Can you please open a bug report about it? [https://github.com/cyberbotics/webots/issues/new?assignees=&labels=&template=bug\_report.md](https://github.com/cyberbotics/webots/issues/new?assignees=&labels=&template=bug_report.md)

##### AJ121 02/12/2021 21:32:21
Can't I set a motor speed more than 10 in webots? It shows error ! My bot is too slow

##### Stefania Pedrazzi [Cyberbotics] 02/15/2021 07:11:58
`@AJ121` the maximum speed for a robot is defined in the `Motor.maxVelocity` field. You should check the value for your robot model and adjust it:

[https://www.cyberbotics.com/doc/reference/motor#field-summary](https://www.cyberbotics.com/doc/reference/motor#field-summary)

##### JMarple 02/16/2021 18:50:55
I wish I had more information (cause I know just saying something is broken isn't all that helpful) but I figure I'd atleast pass this along: 

Running webots on Mac OSX 10.15.6 on MBP 2020, I've been finding webots crashes quite regularly.  It can be just from starting a simulation, or moving a part within the UI, or doing nothing at all, it'll just randomly crash.  Maybe a frequency of once per hour or so. 



Is there any sort of verbose mode or *something* I can do to help track down these issues?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/16/2021 19:05:53
Hello `@JMarple`. You can compile Webots in debug mode and you should get more details about the crashes:

[https://github.com/cyberbotics/webots/wiki/macOS-installation](https://github.com/cyberbotics/webots/wiki/macOS-installation)



Please let us know if you have any problems compiling Webots.

##### Ariel 02/16/2021 20:50:48
Hello! I am writing a program in webots for an autonomous vehicle moving around a specific path and collecting data (images) of the environment. I added a fog node on the scene and the cameras that I had already can not see it. Is this the expected behaviour?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/16/2021 21:34:54
Hello Ariel, it sounds wrong. Can you open an issue about that:

[https://github.com/cyberbotics/webots/issues/new?assignees=&labels=&template=bug\_report.md](https://github.com/cyberbotics/webots/issues/new?assignees=&labels=&template=bug_report.md)

##### DrVoodoo [Moderator] 02/18/2021 15:05:40
`@Ariel` is the fog node declared before or after the camera in the world file? If it's after, try moving the Fog node to before the camera nodes.

##### Ariel 02/18/2021 19:48:53
I thought the fog node doesn't need to be declared at all?


in the controllers code file if this is what you mean

##### Srivastav\_Udit 02/18/2021 22:38:30
Hi, I wanted to know if there was any sample code available for a robot that has to detect, collect and drop an object at a particular location? I'm trying to work towards a swarm implementation eventually. Any help will be much appreciated. Cheers!

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 16:56:46
`@Simon Steinmann` Regarding the RAM usage. I have just tried add the UR5 in the empty world and uses like 850MB of RAM. It seems the complex models cause the high RAM usage.

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 16:57:58
340 -> 1.250 -> 520    (ram usage for your described scenario)


however after a while the 520 trickles down to a bit above 400


I used the UR10e back in the day for my RL testing


it sounds like there is lots of room for optimization. A Model that has 50MB at most in model data, should not need 500MB of RAM (at least in my uninformed opinion 😄 )

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:05:21
Let's say you have 8 core CPU with 16 thread. To reach the full CPU utilization you should run 8 instances (2 processes per instance, 1 Webots process + 1 controller process). Let's say your simulation uses 1GB of RAM. That means that Webots parallel simulations will use 8GB of RAM in total. Considering that typical 8 core PC is equipped with 16GB of RAM (and RAM is cheap) then running the parallel simulations should be fine?

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:06:38
If it were just the simulation, yes, but running deep neural networks takes lots of resources as well

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:08:18
True, in my tests, the RL controllers use like above 1GB of RAM per instance. But then, it is not fair to expect from simulation to use significantly less RAM.

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:08:21
plus, if we were to scale it up on server level, the amounts of RAM get huge. A partner company in our past research project had a 128 core 256 thread server.

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:10:07
I am trying to understand how import is to invest time in the RAM/VRAM optimization.

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:11:25
well, same simulation and setup in Pybullet were in the order of 100-300MB RAM

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:11:45
Per instance?

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:11:48
yes


i have to check if I find the detailed data


Overall RAM usage during the benchmark run: Webots 1811 MB, PyBullet 927 MB


dont have a more detailed breakdown, but I think around 600-700MB is the portion without the simulation, so Pybullet was 200-300MB


Webots has many great advantages for RL, but this is a major downside.


PyBullet has the advantage, that you only initialize what you need, and the simulation is run on a server, completely seperate from a client, which you can connect or not. This is not super userfriendly of course (where Webots shines). But for more complex training scenarios, this is quite important

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:20:53
But quality of the Webots model is much higher (pybullet model in the image)
%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/813822706759499826/unknown.png)
%end



%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/813822734861598720/unknown.png)
%end

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:21:27
we used the same model


directly urdf in PyBullet, conversion in Webots


the resulting protos is 9.282 KB

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:39:10
I have just done some tests with UR5e in an empty world:

- 850MB with initial setup,

- 400MB with all graphics enchantments turned off,

- 177MB with meshes removed, and

- probably less with textures disabled (haven't tried).



Does it make sense that we add a CLI argument to disable meshes and textures?

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:39:37
CLI argument?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:40:05
Yes, something like, `webots --disable-meshes`

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:40:17
being able to run a simulation with all graphics turned off, would be fantastic. There is tons of simulation, where no visual sensors are needed


YES


please! THIS!

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:40:35
Nice! 😄


Can you make an issue about this?

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:41:30
you already described it better :p


but I can open it if you want

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:41:55
Ahh, ok, I will 🙂

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:42:01
thx ^^


what does this mean?


- 400MB with all graphics enchantments turned off,


I mean

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:43:34

%figure
![unknown.png](https://cdn.discordapp.com/attachments/565155651395780609/813828413441441802/unknown.png)
%end

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:43:43
ohhhhhhhhhhh


yeah


perhaps it could be launched with no rendering? And only load those things, when a different mode is selected


but your suggestion is already super great 🙂

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:49:04
[https://github.com/cyberbotics/webots/issues/2777](https://github.com/cyberbotics/webots/issues/2777)

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:50:20
would this include the graphics settingsß


?


perhaps "no render" mode can turn those off, if it frees up ram

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:50:58
`--no-rendering` still renders meshes for the camera based devices, so it is not the same

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:51:14
ohhh makes sense

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:51:25
Add a comment 🙂

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:55:12
no textures and no meshes, does it still render? just with the bounding objects?

##### Darko Lukić [ROS 2 Meeting-Cyberbotics] 02/23/2021 17:56:48
Yes

##### Simon Steinmann [ROS 2 Meeting-Moderator] 02/23/2021 17:57:16
thx

