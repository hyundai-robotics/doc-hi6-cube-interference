# ${cont_model} Controller Cube Interference Check Manual

{% hint style="warning" %}
The information presented in this manual is the property of HD Hyundai Robotics.

The manual may neither be copied, in part or in full, nor redistributed without prior written consent from HD Hyundai Robotics.

It may neither be provided to any third party nor used for any other purposes.



HD Hyundai Robotics reserves the right to modify this document without prior notification.



**Copyright ⓒ 2023 by HD Hyundai Robotics**
{% endhint %}



# About the Manual

This manual explains the fundamentals, structure, and application methods of the cube-interference check function of the HD Hyundai Robotics ${cont_model} controller. Each chapter describes not only the basic operation procedures but also how to use simple application functions.

This manual may be provided as a reference for customers who have purchased HD Hyundai Robotics products, or used as internal training material for educational purposes.

Because this manual is based on standard specifications, some details may vary depending on the model of the product you purchased. In addition, the contents and specifications of this manual are subject to change without notice for performance improvements. HD Hyundai Robotics is not responsible for any issues arising from inaccurate information or typographical errors. For detailed information on revisions, please visit our website\(www.hyundai-robotics.com\).



# 1. Overview

## 1.1 Purpose of the Function

- Prevent multiple robots from simultaneously entering the same cube area during playback.  
- When the robot’s tool center point (TCP) enters a defined cube area, a signal is output, allowing the user to utilize this signal for various applications.

![Overview](../_assets/schematic_diagram.png)

## 1.2 Scope of the Function

### 1) When the robot program is running
- If the robot’s TCP is inside the defined cube area, the assigned output signal turns **ON**; if it is outside, the signal turns **OFF**.  
- When a robot’s TCP enters the cube area, or when its step target position enters the cube area during step execution, that robot gains priority for the area and outputs a cube-entry signal (left robot in the figure above).  
- The cube-entry output signal is received by the other robot (right robot in the figure above) as a cube-prohibition input signal, and the receiving robot automatically stops when cube interference is expected.  
- When the robot that first entered the cube area completes its operation, the waiting robot automatically restarts.

### 2) When the robot is jogging or stopped
- In Manual Mode (Jog), the system detects the TCP position and outputs the cube-entry signal.  
- During jog operation, even if the cube-prohibition input signal is received, the robot **does not** automatically stop, so caution is required.

## 1.3 Limitations of the Function

This function is designed to automatically stop the robot when simultaneous entry into a cube is expected and to automatically restart once the cube-prohibition input signal is cleared.

However, even if the robot decelerates as much as possible when a cube-prohibition input signal is detected, there may be cases where simultaneous entry into the cube area cannot be avoided. This situation is called a **dead-lock**.

A dead-lock may occur if the cube-entry output signal and cube-prohibition input signal are incorrectly connected for a shared cube, or due to communication delays between two robots. In such cases, both robots may enter the cube area simultaneously, resulting in an error:  
**E0222 – Same cube simultaneous entry detected**.

- A dead-lock condition may occur when two robots attempt to enter a shared cube area simultaneously.  
- Automatic avoidance of dead-lock or automatic return-to-home recovery is **not supported**.  
- This function **cannot** be used in conjunction with the Arm Interference Detection function.# 2. Related Functions

## 2.1 Cube Area Settings

Select:『System』 → 『4: Application Parameter』 → 『7: Cube inteference check』

![Cube Settings](../_assets/fig1_dst_dialog.png)

You can add or remove cube conditions using the **(+)** or **(–)** buttons on the right side of the screen.  
For each individual cube condition, configure whether it is enabled, assign the output signal for cube-entry detection, and set the input signal used to prohibit entry into the cube.
## 2.1 Cube Area Setting Methods

Two methods are provided for defining a cube area.

---

### • Diagonal Point Method
- This method defines the cube by specifying two diagonal points of the hexahedron.  
  As shown in the illustration, you manually enter the starting and ending diagonal positions.

### • Center Point Method
- This method defines the cube by specifying the cube’s center point and the distances in the X, Y, and Z directions.
### 1) Cube Area Setting Methods

Two methods are provided for defining a cube area.

---

### • Diagonal Point Method

- This method defines a cube by specifying two diagonal points of the hexahedron.  
  As shown in the figure below, you directly enter the starting and ending diagonal positions.  
- To record the robot’s current TCP position, place the cursor on the **<Start Position>** or **<End Position>** button and press **ENTER**.  
  The current TCP position will be saved as the selected position.

**Example Setting**

<p align="center">
  <img src="../_assets/cube_diag_points.png" />
  <img src="../_assets/diag_pints2.png" />
</p>
### • Method for Setting the Center Point

- The center point is defined by specifying the cube’s center position and the distances in the X, Y, and Z directions.  
- To record the center point as the robot’s current TCP position, place the cursor on **<Center Position>** and press **ENTER**.  
  The current TCP position will be saved as the center point.

**Example Setting**

<p align="center">
  <img src="../_assets/center_point.png" />
  <img src="../_assets/center_point2.png" />
</p>

Record the center point of the cube area, then specify the distances in the X, Y, and Z directions from that center point.
### • Setting the Coordinate System Number

You can specify the position of the cube in space using either the base coordinate system or a defined user coordinate system, depending on the cube setting method.  
If the coordinate system number is set to **“0”**, the cube area is configured using positions defined in the **base coordinate system**.  
If the coordinate system number is **“1” or higher**, the cube area is defined using positions based on the corresponding **user coordinate system**.

> **Note**  
> Even if you change the coordinate system, the positions defined for the cube area do **not** update automatically.  
> Therefore, the cube may be assigned to a location different from what the user intended, so caution is required.

![Cube Setting](../_assets/fig1_dst_dialog.png)

- When using a **user coordinate system**, both the diagonal point and the center point **must be defined within the user coordinate system**.

<img src="../_assets/user1.png" width="40%"/>
<img src="../_assets/user2.png" width="44%"/>
### • Cube I/O Signal Settings

- **Cube-entry Output Signal**:  
  This signal indicates whether the robot itself has entered the designated cube area.  
  Assign the appropriate signal number to the cube-entry output signal field.

- **Cube-prohibition Input Signal**:  
  This signal number is assigned to receive an input when another robot enters the same cube area.

In the example shown above, the common cube area shared by the two robots is **Cube 2 of Robot 1** and **Cube 1 of Robot 2**.  
In this case:

- Connect **Robot 1 – Cube 2 (Cube-entry Output Signal)** → **Robot 2 – Cube 1 (Cube-prohibition Input Signal)**  
- Connect **Robot 2 – Cube 1 (Cube-entry Output Signal)** → **Robot 1 – Cube 2 (Cube-prohibition Input Signal)**

<p align="center">
  <img src="../_assets/common_cube.png" />
</p>
## 2.3 Example of Creating and Executing a Work Program

![](../_assets/cube_interfer_example.png)

Set cube areas of the same size at the same spatial location for each robot.  
At this time, ensure that the cube positions are defined according to the coordinate system selected for each robot.

---

### **• Robot 1: Cube-entry Output Signal ON, Robot 2: Cube-prohibition Signal ON**

![](../_assets/exmple1.png)

When Robot 1’s target position lies inside the defined cube area, the cube-entry output signal turns ON.  
Even if the target position is not inside the cube, the signal will turn ON if the robot enters the cube area during movement.

---

### **[Robot 1 Example]**

- In this example, steps S4 to S7 are assumed to enter the cube area.

![](../_assets/exmple2.png)

To prevent dead-lock caused by simultaneous cube entry with the other robot,  
**the step immediately before entering the cube must be set as a non-continuous step (A = 0).**  
Alternatively, commands such as **WAIT** or **DELAY** may be used before entering the cube to intentionally create a non-continuous condition.

---

### **[Robot 2 Example]**

- In this example, Robot 1 (R1) is already inside the designated cube area, and Robot 2 attempts to enter the cube area defined in steps S4 to S7.

![](../_assets/exmple3.png)

If the other robot is already inside the cube area, or is moving with the intention of entering it, the cube-prohibition signal (**di8**) is received.  
If Robot 2’s next target position lies inside the cube area, Robot 2 will stop and wait.  
During this waiting state, the teach pendant displays the message:  
**“Waiting cube entry.”**

Once the other robot exits the cube area, Robot 2 automatically resumes operation.

# 3. Error Detection

| Possible Cause of Error | This occurs when the cube-prohibition input signal is received while the robot is already inside the cube area. |
| :--- | :--- |
| Error Message | E0222 Same cube simultaneous entry detected |
| Corrective Actions | 1) Jog the robot outside the cube area, and then restart it. <br> 2) Modify the program to prevent this error from occurring. <br> - Set the step immediately before entering the cube as a non-continuous step. <br> - Use a WAIT command to perform additional interlocks right before cube entry. |

# Appendices

  


# Rules on Occupational Safety and Health Standards, and Notice for Safety Inspection

The industrial robot should be installed in consideration of the inspection standards both of the Rules on Occupational Safety and Health Standards and of the Notice for Safety Inspection \(if subject to inspection\).

"[Rules on Occupational Safety and Health Standards](https://hrbook-hrc.web.app/#/view/rules-on-occupational-safety-and-health-standards/english/README)"
# Quality Assurance

"[Quality Assurance](https://hrbook-hrc.web.app/#/view/quality-assurance/english/README)"
