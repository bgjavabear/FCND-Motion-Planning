## Project: 3D Motion Planning

![Quad Image](./misc/enroute.png)

---

# Required Steps for a Passing Submission:

1. Load the 2.5D map in the colliders.csv file describing the environment.
2. Discretize the environment into a grid or graph representation.
3. Define the start and goal locations.
4. Perform a search using A* or other search algorithm.
5. Use a collinearity test or ray tracing method (like Bresenham) to remove unnecessary waypoints.
6. Return waypoints in local ECEF coordinates (format for `self.all_waypoints` is [N, E, altitude, heading], where the
   drone’s start location corresponds to [0, 0, 0, 0].
7. Write it up.
8. Congratulations!  Your Done!

## [Rubric](https://review.udacity.com/#!/rubrics/1534/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one. You can submit your writeup as markdown or pdf.

You're reading it! Below I describe how I addressed each rubric point and where in my code each point is handled.

### Explain the Starter Code

#### 1. Explain the functionality of what's provided in `motion_planning.py` and `planning_utils.py`

Let's start with planning_utils.py

1. class Action: contains set of actions that drone may perform. Each action has 3 attributes: y,x,cost where y is a
   change in a y-axis, x is a change in an x-axis, cost is a cost of action

2. valid_actions method: based on grid, and your current position it identifies what actions you can perform. For
   example, you cannot move right if there is an obstacle there, so the list of valid actions won't have EAST action

3. a_star method: implementation of A* search algorithm

4. heuristic method: Implementation of heuristic

5. create_grid method: creates grid with 0 and populates it with obstacles using colliders.csv file

### Implementing Your Path Planning Algorithm

#### 1. Set your global home position

```python
import csv

with open('colliders.csv', newline='') as f:
    reader = csv.reader(f)
    first_line = next(reader)
    lat0, lon0 = float(first_line[0].split(' ')[1]), float(first_line[1].split(' ')[2])
```

I read the first line of csv, then split it, then split it again to retrive numerical values and converted it to float.
Afterwards I set home position using self.set_home_position

#### 2. Set your current local position

I retrieved lon0, lat0 for csv file, used them as home position using set_home_position method. 
After that I retrieved global position of my drone and converted it to local position using global_to_local method.

#### 3. Set grid start position from local position

```python
grid_start = (-north_offset + int(local_position[0]), -east_offset + int(local_position[1]))
```

#### 4. Set grid goal position from geodetic coords

```python
        goal_lat = 37.792
        goal_lon = -122.397
        goal = [goal_lon, goal_lat, 0]
        local_goal = global_to_local(goal, self.global_home)
        grid_goal = (-north_offset + int(local_goal[0]), -east_offset + int(local_goal[1]))
```

I specified latitude and longtitude and using global_to_local method converted them to local coordinates.
I tested the result, everything works as expected.

#### 5. Modify A* to include diagonal motion (or replace A* altogether)

```python
WEST = (0, -1, 1)
EAST = (0, 1, 1)
NORTH = (-1, 0, 1)
SOUTH = (1, 0, 1)
NORTH_EAST = (-1, 1, np.sqrt(2))
NORTH_WEST = (-1, -1, np.sqrt(2))
SOUTH_EAST = (1, 1, np.sqrt(2))
SOUTH_WEST = (1, -1, np.sqrt(2))
```

I added 4 additional enums, corresponding to NORTH_EAST, NORTH_WEST and etc directions. cost is sqrt(2) because it is a
hypotenuse of a triangle with sides 1 and 1.

#### 6. Cull waypoints

I added prune_path method to planning_utils.py file. It takes 3 points in path and checks them for collinearity. If
those 3 points could be placed on the same line, we remove the middle point and continue the process with the next 3
points.

### Execute the flight

#### 1. Does it work?

It works!

### Double check that you've met specifications for each of the [rubric](https://review.udacity.com/#!/rubrics/1534/view) points.

# Extra Challenges: Real World Planning

For an extra challenge, consider implementing some of the techniques described in the "Real World Planning" lesson. You
could try implementing a vehicle model to take dynamic constraints into account, or implement a replanning method to
invoke if you get off course or encounter unexpected obstacles.


