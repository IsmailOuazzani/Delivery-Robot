# Robot Platform
We worked with a TurtleBot3 Waffle Pi equipped with the Pi Camera, pointed downward to examine
the area underneath the robot. The camera is useful when mounted this way as it can be used for line
following and floor color detection. The different sensors (Camera and motors) communicated with
each other through a ROS infrastructure that was provided to us. We were provided with the code for perception and data extrapolation from the Pi camera.

# Solution Strategy
## Line following
We use the Pi camera to detect the current color the robot is on. If the color detected is black, we expect
it to be on the line, otherwise, we assume the robot to be in an office.

To follow the line we use the camera to determine whether the line is centered in the frame. If the
line is not, we assume that the robot’s trajectory needs to be corrected and we use a PID controller
to perform the correction. If properly tuned, we assume the PID controller to always maintain the
robot centered on the line.

If the color detected by the camera is not black, we assume the robot has reached an office. As
the offices can be crossed by traveling forward in a straight manner, we decided to turn off the PID
in these areas to avoid twitching. The offices are uniformly colored and span the whole field of view
of the camera. It is therefore impossible for the robot to correct its trajectory given the image shown.
Leaving the PID controller on when the robot is in an office, would imply uncontrollable path deviation.
Turning off the PID controller in the offices, implies blindly moving forward for some time. If our
PID controller is properly tuned, the robot will be centered on the black line before entering an office,
and it will therefore find the black line again after exiting the office, as the black line doesn’t change
direction within the office span.

## Robot localization
For Robot localization we use Bayesian localization. Once we
encounter an office we use the Bayesian model to keep track
of the previous colors and predict the current office given the
current color measured. We initialize the initial position to
be uniformly probable across all the states since the robot can
start from any location. We expect the model to converge to the
right position after some offices have been visited. We therefore keep looping around the track until the model becomes
confident enough in its current state.




