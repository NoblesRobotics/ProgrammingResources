# FTC Programming Reference

## Control system

In FTC, the control system is made up of a Control Hub, which is mounted on the robot, and a Driver Station, which sits outside the robot competition area and remotely connects to and commands the Control Hub. A max of two gamepads are connected to the Driver Station to allow human drivers to drive and control the robot.

## Flow of a competition match

1. Robot is placed in competition area; driver station is placed on designated stand outside.
2. Driver uses the driver station to select an autonomous "opmode" (Java program) and presses an INIT button; some initialization code gets run
3. When instructed by the ref, driver presses start button; autonomous opmode runs for 30 seconds. Drivers cannot use the gamepads.
4. When instructed by the ref, driver selects a tele-op opmode and presses INIT and play; tele-op opmode runs for 2 minutes. Drivers use the gamepads for this section.
5. Driver hits stop button on driver station; match ends

## Basic opmode structure

```java
@TeleOp
public class SampleOpMode extends LinearOpMode {
  @Override
  public void runOpMode() {
    
    // code to run when driver presses INIT

    waitForStart();

    while ( opModeIsActive() ) {
      
      // code to run after driver presses start button and before driver presses stop button

    }
  }
}
```

## Initialization code

All robot devices are given a name through the settings on the driver station. For example, in the following code which gets a reference to a motor, the motor's name is "ma":

`DcMotor frontLeftMotor = hardwareMap.get(DcMotor.class,"ma");`

### Motor options

Usually, the motors on the left side of the robot will have their +s and -s switched. This makes them run backwards, which can be counteracted like this:

`frontLeftMotor.setDirection(DcMotor.Direction.REVERSE);`

Encoders inside each motor track exactly how much it has spun. To use them correctly, each motor's encoders need to be properly initialized:

`frontLeftMotor.setMode(DcMotor.RunMode.STOP_AND_RESET_ENCODER);`

By default, when the code stops supplying power to a motor, the robot won't try to stop its rotation. This code will make the robot brake on zero power:

`frontLeftMotor.setZeroPowerBehavior(DcMotor.ZeroPowerBehavior.BRAKE);`

### Other devices

Referencing a servo: `Servo clawServo = hardwareMap.get(Servo.class,"sa");`

Referencing a distance sensor: `DistanceSensor distanceSensor = hardwareMap.get(DistanceSensor.class,"da");`

## Main loop code

### Motors

Basic usage: `frontLeftMotor.setPower(1.0);` Power must be between -1 and 1.

If you reset the encoders during the init code, you can now turn them on: `frontLeftMotor.setMode(DcMotor.RunMode.RUN_USING_ENCODER);` (Note: you should do this just before the `while ( opModeIsActive() )` loop because you only need to do it once.)

Then you can use `frontLeftMotor.getCurrentPosition()` to see the net rotation of the wheel since the start of the opmode. It is important to note that the output of this function will be in encoder ticks which vary from motor to motor. The motors we usually use have 537.6 ticks per revolution, and the mecanum wheels we use have a diameter of 96mm (and a circumference of pi * 96mm).

### Servos

`clawServo.setPosition(1.0);` Position must be between 0 and 1.

`clawServo.getPosition()` returns the position in the range from 0 to 1.

### Distance sensor

`distanceSensor.getDistance(DistanceUnit.INCH)` returns the observed distance. Note that this function will behave erratically if nothing is observed in the field of view of the sensor so be sure to handle invalid results (the field is a 12ft x 12ft square so anything over 204in. is definitely invalid).
