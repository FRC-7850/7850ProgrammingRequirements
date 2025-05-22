# 7850 Programming Requirements
This document is to provide a standard for programming for Team 7850 based in Coon Rapids, Minnesota. 

Affected users include the students and mentors involved in wiring and programming our team robots, whether for competition or otherwise.

This document is not currently fully inclusive of all wiring or programming requirements. It will focus on programming requirements and electrical requirements for CAN circuits and motor controller peripherals.

1. Git Requirements
2. CAN Requirements
3. Command Based Programming
4. To Be Determined

## 1. Git Requirements
  
### **_Use Branches for your changes_**
- Every change shall be done on a branch off of the main branch
  - Users shall not commit directly to main branch.  
  - Main branch shall be the latest robot-ready code.
- Users shall push any changes on the branch they are working on to Github at the end of the day.
</details>

## 2. CAN Requirements
  
### **_Wiring Requirements_**  
  - CAN circuits shall be wired with 22 AWG Green and Yellow twisted pair wires.  
  - Twisted pair wires shall have at least 2 twists per inch of wire.
    - Green wire shall be used for CAN Low.
    - Yellow wire shall be used for CAN High.
  - Twisted pair wires shall have at least 2 twists per inch of wire.
  - CAN wires shall be terminated using [JST SM](https://www.jst-mfg.com/product/pdf/eng/eSM.pdf) series connectors, with pins SYM-001T-P0.6 and SHF-001T-0.8BS.
  - CAN wires shall be inserted into the connectors such that CAN High is Pin 1 and CAN Low is Pin 2.
  - CAN circuit shall begin at the RIO or Robot Controller.
  - RIO or other Robot controller shall be equipped with a "plug" type connector.
  - All drive train controllers and any IMUs shall be wired into the CAN circuit immediately after the Robot Controller.
  - CAN circuit shall end at the Power Distribution Panel.
  - Power Distribution Panel shall be equipped with a "Recepticle" type connector.

### **_CAN ID Requirements_**

  - CAN ID 0 shall be reserved for the robot controller.
  - CAN ID 200 shall be reserved for the Power Distribution Panel.
  - CAN IDs 1-8 are reserved for the drivetrain.  
    - All drive motors shall be even CAN IDs.  
    - All steering motors shall be odd CAN IDs.  
    - The drive modules shall start with the front-left module with the lowest CAN IDs (1 and 2) and assign CAN IDs working counter clockwise and ending with the front right modules using the highest CAN IDs (7 and 8)
      
> [!TIP]
>  - Example: Tank drive with 2 modules, each with 2 drive motors  
>    -   Front Left motor uses ID 2  
>    -   Back Left motor uses ID 4  
>    -   Back Right motor uses ID 6  
>    -   Front Right motor uses ID 8
>      
> - Example: Swerve drive made of 4 swerve modules, each containing a steering and drive motor  
>   -   Front Left module uses CAN ID 1 for steer motor, and CAN ID 2 for drive motor  
>   -   Back Left module uses CAN ID 3 for steer motor, and CAN ID 4 for drive motor  
>   -   Back Right module uses CAN ID 5 for steer motor, and CAN ID 6 for drive motor  
>   -   Front Right module uses CAN ID 7 for steer motor, and CAN ID 8 for drive motor
   
- CAN ID 9 is reserved for the IMU or Gyro.
- CAN IDs 10-19 are reserved for future use.
- CAN IDs 20-69 are reserved for subsystems.
  - Each subsystem shall use an independent group of 10 IDs
  - Subsystems shall ID modules and sensors from lowest ID to highest from either Left to Right, Front to Back, Top to Bottom, CCW starting at front, or a combination of the 4 methods, whichever makes most sense.
- All follower motor controllers shall follow a controller with a lower CAN ID than itself.
- The following table shall be followed to assign all CAN IDs.
    
  |Assigned CAN ID|Module|
  |:------|--:|
  |0|Robot Controller|
  |1|Drivetrain motor (LF Steer)|
  |2|Drivetrain motor (LF Drive)|
  |3|Drivetrain motor (LB Steer)|
  |4|Drivetrain motor (LB Drive)|
  |5|Drivetrain motor (RB Steer)|
  |6|Drivetrain motor (RB Drive)|
  |7|Drivetrain motor (RF Steer)|
  |8|Drivetrain motor (RF Drive)|
  |9|IMU/Gyro|
  |10-19|Reserved|
  |20-29|Subsystem 1|
  |30-39|Subsystem 2|
  |40-49|Subsystem 3|
  |50-59|Subsystem 4|
  |60-69|Subsystem 5|
  |200|Power Distribution Panel|

>[!NOTE]
>Any 3<sup>rd</sup> party products that are installed on the CAN Bus may require a specific CAN ID. In these cases, the programming team must agree on what changes to the CAN ID assignments are needed to accomodate the product.
  
## 3. Command Based Programming 
### **_Use Command Based Where Possible_**

- Robot code shall be written as command based whenever possible  
  - Exceptions will be made on a case by case basis depending on circumstances and programming team's desires  

### **_Use Smart Subsystems_**

- Subsystems are a group of 2 or more actuators or sensors that can be used together to create complex responses.  
- Subsystems shall be created for every major functional component of the robot.
  - Drivetrain will always be its own subsystem.
- A subsystem class file shall be created for each subsystem on the robot.

### **_Use of Commands_**

- Commands are instructions given to the robot using a subsystem.
- Commands, for the purpose of this document, are defined into 3 groups:
  - Complex Commands
  - Simple Commands
  - Single Use Commands
 
- Complex commands are commands which require an initialization, execution, and ending action, or a command where we need to know exactly when it ends.
  - Complex commands shall be written in their own class file stored in the Commands folder.
- Simple commands are used in the code more than once, and do not meet the requirements of a complex command.
  - Simple commands shall be written as funtions in the subsystem class file.

 ```java
  public Command driveMotor(double speed) {
     return runOnce(
         () -> {
            setSpeed(speed);
         });
   }
 ```
- Single Use commands are used once in the code, and do not meet the requirements of a complex command.
  - Single Use commands shall be written as a lambda function where it is used.

 ```java
   runOnce(
      () -> {
         exampleSubsystem.setSpeed(0);
      });
 ```

- All subsystems shall have a default command set which will result in no movement when the controller is set down.
```java
  public SingleMotorSubsystem() {
    setDefaultCommand(driveMotor(0));
  }

```
>[!TIP]
>A default command to achieve this may be setting motor speeds to 0, setting motor speed to a joystick axis, or a PID holding the actuator position.
>
>There may be other ways of accomplishing no movement that can be used.


