# 3. Error Detection

| Error Message | E0222 Same cube simultaneous entry detected |
| :--- | :--- |
| Possible Cause of Error | This occurs when the cube-prohibition input signal is received while the robot is already inside the cube area. |
| Corrective Actions | 1) Jog the robot outside the cube area, and then restart it. <br> 2) Modify the program to prevent this error from occurring. <br> - Set the step immediately before entering the cube as a non-continuous step. <br> - Use a WAIT command to perform additional interlocks right before cube entry. |

