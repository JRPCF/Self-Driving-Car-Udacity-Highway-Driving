# Model Documentation
## How to approach the Car Model

My implementation follows the Q&A implementation with a simple state machine for the lane choice.
I start with a sensor fusion section. I have two global variables double ref_vel (representing the vehicle velocity) and int lane (representing the lane), these preserve their value during the entire program run. I also have three boolean flags, too_close, not_turn_right, and not_turn_left which reset to false every 20ms.

During my sensor fusion, I check every car in sensor readings and use their frenet coordinates to find how they interact with our car. If there is a car on the left, not_turn_left is set to true. If there is a car on the right, not_turn_right is set to true. If there is a car in front of our car, too_close is set to true. The range is 20 ahead of our car and 10 behind for turns of same distance for our own lane.

Then there is the state machine phase. Here, if too_close is true, we turn to the left lane if available, if not but the right lane is available we turn to the right, if not we decrease our speed. When the vehicle is in the leftmost lane it cannot turn left. When the vehicle is in the rightmost lane it cannot turn right. When the vehicle is no longer too close, it attempts to return to the middle lane (if possible).

Then there is the lane generation portion that is identical to the Udacity tutorial. Here we assign the x, y, and theta coordinates of our car to temporary values. Using these values and depending on whether we have a historical trajectory, we either plot 2 future points in the car's trajectory or we extract the final two points from our historical trajectory. We then utilize our desired lane and our desired velocity to plot 3 points: one 30 away, one 60 away, and one 90 away. We then transform these trajectory coordinates to the coordinates of the car. Finally, we use the spline.h library to create a smooth trajectory that passes through each of these 5 points. Using this spline trajectory, we extract points at a specific velocity in the trajectory such that each point is 0.2ms away from the previous point. 

## Reflection

The simplistic state machine that does not plan the motion of the car has a few flaws. if a car attempts to change lanes onto our lane, we would not be able to account for this. A much more intelligent but complex algorithm would consider predicting car locations. If a car is moving slower ahead of us, the car would slow down until the other car is no longer in range. This means, instead of reducing the speed to match the car ahead, the car would frequently decelerate and accelerate, which is not ideal.
