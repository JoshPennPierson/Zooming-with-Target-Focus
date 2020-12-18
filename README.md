# Zooming-with-Target-Focus
This is an algorithm that maintains the proportions between a given object and the edges of the view while zooming.

Try out the algorithm here (using the mouse as the focal object and mouse scroll to zoom): [http://pennpierson.com/gm_zoom/index.html](http://pennpierson.com/gm_zoom/index.html)

Or download and play around with the with the GameMaker source files.

----

Algorithm Steps:

1) Find the proportional position of the object.
2) Apply zoom to view.
3) Set the view x and y origin coordinates such that the proportional positioning is maintained for the object.

![Example](https://github.com/BflySamurai/Zooming-with-Target-Focus/blob/master/Graphics/Zooming_with_Target_Focus_0.png?raw=true "Example")

![Example](https://github.com/BflySamurai/Zooming-with-Target-Focus/blob/master/Graphics/Zooming_with_Target_Focus_1.png?raw=true "Example")

*Both the X and Y values are calculated the exact same way, so to save space and for clarity, only the X values are calculated/shown here.*

* X<sub>O</sub> ~~ View x origin
* X<sub>V</sub> ~~ View width
* X ~~ Focus object x coordinate
* X<sub>A</sub> ~~ Distance between X<sub>O</sub> and X
* X<sub>P</sub> ~~ Proportional distance between X<sub>O</sub> and X
* Zoom ~~ Amount of zoom
* Default_X ~~ Width of view when zoom is 1

 | | X Origin | View Width | Object X | Distance to origin | Proportional distance to origin
 --- | --- | --- | --- | --- | ---
Default Zoom | **X<sub>O</sub>** | **X<sub>D</sub>** | **X** | **X<sub>A</sub>** = X / X<sub>O</sub> | **X<sub>P</sub>** = X<sub>A</sub> / X<sub>D</sub>
Applied Zoom | **X<sub>O<sub>zoom</sub></sub>** = X - X<sub>A<sub>zoom</sub></sub> | **X<sub>V</sub>** = X<sub>D</sub> * Zoom | Constant | **X<sub>A<sub>zoom</sub></sub>** = X<sub>V</sub> * X<sub>P</sub> | Constant

# GameMaker Studio Code

Below is the GML code for a camera object. The target is set to obj_cursor, which is a GameMaker object that updates every step to be at the mouse position). You can set the target to whatever you want, or you can swap out some of the code to have it directly follow the mouse.

```
// INFO
// view_xview[0] ~~ x origin of view
// view_yview[0] ~~ y origin of view
// view_wview[0] ~~ width of view
// view_hview[0] ~~ height of view
```

## Create Event

```
/// Initialize variables

zoom = 1;             // Initial zoom level
default_width = 400;  // Initial view width
default_height = 300; // Initial view width
zoom_increment = 0.1; // How much to zoom by
zoom_max = 10;        // Maximum zoom amount
target = obj_cursor;  // Target to keep focus on when zooming
```

## Step Event

```
/// Get inputs

mouse_scroll_up = mouse_wheel_up();
mouse_scroll_down = mouse_wheel_down();
```

```
/// Zooming

var zoom_has_changed = false;

// Calculated desired zoom level
var desired_zoom = zoom + ((mouse_scroll_up - mouse_scroll_down) * zoom_increment);

// Apply desired_zoom to zoom if it is above zero and below the max
if new_zoom > 0 and new_zoom < zoom_max {
    zoom = desired_zoom;
    zoom_has_changed = true;
}

// Apply zoom to the view
if zoom_has_changed {
    // Get the relative position of the target on the screen.
    target_x_dist = target.x - view_xview[0];
    target_y_dist = target.y - view_yview[0];

    // Calculate how far the object is across the screen (as a percent) for both x and y values.
    target_x_p = target_x_dist / view_wview[0];
    target_y_p = target_y_dist / view_hview[0];

    // Set width and height of the view to match the zoom level
    view_wview[0] = default_width * zoom;
    view_hview[0] = default_height * zoom;

    // Use the previous percent values to calculate where the target should be on the screen.
    target_x_new_dist = view_wview[0] * target_x_p;
    target_y_new_dist = view_hview[0] * target_y_p;

    // Set the origin of the view such that the target is in the correct place on the screen.
    view_xview[0] = target.x - target_x_new_dist;
    view_yview[0] = target.y - target_y_new_dist;
}
```
