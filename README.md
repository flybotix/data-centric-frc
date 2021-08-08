# data-centric-frc

## ILITE's Modules
Our modules work very similarly to other teams' modules. Each module represents a subsystem, and each subsystem has multiple pieces of hardware to control. Furthermore, there is a controller which determines how to interpret robot operator controller inputs (from something like a joystick or Xbox controller) to module outputs.

The code to control modules is pretty simple:
![image](https://user-images.githubusercontent.com/4370608/128266743-45075ab5-fcbb-4958-b34f-e4622ea12931.png)
```java
  mRunningModules.readInputs();
  mActiveController.update();
  mRunningModules.setOutputs();
```

## The ILITE Robot Data Framework

Our robot uses "Modules" that represents the different subsystems on the robot. Each subsystem may have on or more sensors, and one or more motors on that subsystem.

To control each module, the robot runs a specific high-level sequence of method calls.
![image](https://user-images.githubusercontent.com/4370608/128266375-4219db4a-6bf6-4e48-a079-5d45fb1879d3.png)


## Why did we move to this framework?
In high school robotics, a typical "module" looks something like this:

```java
public class Arm {
  private MotorController mMotor;
  public Arm() {
    mMotor = new MotorController(1);
    mMotor.setPID(0.005,0.0,0d);
  }
  
  public void setTargetPosition(double pAngleRad) {
    if(mMotor.getAngle() > pAngleRad - 0.001 && mMotor.getAngle() < pAngleRad + 0.001) {
      mMotor.set(0);
    } else {
      mMotor.getPID().setReference(pAngleRad);
    }
  }
  
  public double getCurrent() {
    return mMotor.getCurrent();
  }
  
  public double getArmAngle() {
    return mMotor.getAngle();
  }
}
```

There is nothing wrong with that code. It works, and it usually works well enough for simple FRC purposes. But what if you wanted to _log_ everything about the `Arm` class? What if, for some reason, all of the `getAngle()` calls overload the CAN bus bandwidth? What if you want to _structure_ the data itself so it's easily stored and retrieved using something like a relational database? (And why would anyone want to do that?). On top of all of that, how can you test the logic that controls `Arm` class with just _repeatable unit tests_ that can be run on-demand using Gradle?

There are a couple of very famous robots, built decades ago, that had to find answers to some of these questions. Part of the answer was a software architecture that generated much of the low-level code[^1] in order to facilitate a repeatable data framework across all robot subsystems.

[^1]https://softwareengineering.stackexchange.com/questions/159637/what-is-the-mars-curiosity-rovers-software-built-in
