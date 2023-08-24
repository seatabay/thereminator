# Thereminator
Light and hand position controlled cheap and accessible theremin.

This project appeared in after 3 hours of brainstorming for an approaching deadline!

Accesing musical instruments are not easy for people all around the globe, therefore we aimed to utilize whatever we have and some people may have too. Initial goal of the project is to utilize objects we may have, a laptop and a smart phone to keep the gesture and interaction of playing an instrument. Theremin is one of the instruments controlled by no physical touch and the distance from two antennas. In this project, the gestures of both hands are preserved and the sound synthesis part has been done with a Pure Data patch.

## Conceptual Design
Theremin is an instrument that goes way back to 1920’s. It’s operating principles
depend on generated electromagnetic fields between two antennas. In the current state,
there are brands such as Moog that are selling compact theremin. But the problem is
that, the affordability and accessibility to this instrument can be an issue to some.
Therefore, in our final project we focused on three aspects:

**1. Technological advancement**
   
As technology advances, new technological tools can become a base for different
designs. In our case, by taking advantage of this, we tried to replace the electromagnetic
field manipulation with light density and gesture detection. A camera on a phone or a
basic application in an OS can work quite well to achieve such an approach.

**2. Keeping the essence**

Theremin has a specific performance technique that involves both hands and their
position from the antennas. One of the questions that popped up in our mind was
“How would it feel to use Theremin techniques but hear a sound in a different
timbre?”. So as one of the future steps, we plan to use relevant software to allow
more variation in sounds.

**3. Accessibility**

Theremin is an instrument that cannot be accessed easily, like a guitar. If you
stumble upon one, you might want to take it home but it will not be easily affordable.
So in our design, we utilize everyday objects which are easily accessible for most of
the people: a laptop and a phone. Even though not everyone has these technological
tools, they are far more reachable and they do not require a specific budget.


## Technical Design
Hardware: Laptop, Android smart phone
Software: Wekinator, PureData, OSCHook, Handpose OSC

![Screenshot from 2023-08-24 02-28-3](https://github.com/seatabay/thereminator/assets/36015029/d4faa743-7227-40b3-8520-6a2c91835b3e)

Thereminator technical design process

**1. OSCHook**

OSCHook is an Android application that utilizes the phone’s sensor abilities, such
as light intensity, orientation and accelerometer, and sends the measurements in form
of OSC messages1. The light intensity can work in a way that detects the distance of
an object or surface from the smart phone. This distance detection is suitable for the
pitch control of theremin, if we look at the original design of it.

**2. HandPose OSC**

HandPose OSC is an Electron app that uses MediaPipe to output OSC messages2.
It is able to track the position of fingers and the shape of the whole hand. In our
project, the movement of the fingers controls the amplitude of the sound.

**3. Wekinator**

Wekinator is a platform which allows the user to train machine learning models for
interactive projects. It sends and receives OSC messages to and from other
applications3. In Thereminator, two different models were trained for controlling the
instrument. One of them is controlling the pitch of the sound and the other one is
controlling the amplitude. As shown in Figure 1, the input data were received from
different devices.

**4. PureData**

PureData is an open source visual programming language for multimedia4. In our
project, the OSC messages being sent from Wekinator is received by PureData and the
sound is synthesized in the patch shown below.

This patch kind of replaces a VST by using a basic oscillator object. Since it is the
very first prototype, the sound design part may vary in the future. The left part of the
patch controls the frequency by receiving light intensity measurements as OSC
messages that are manipulated by controlling the distance between smart phone and
hand. The right part receives OSC messages from HandPose-OSC, which is controlling
the amplitude by opening and closing the left hand.
Training Process
We have trained two different Neural Network models with two different Wekinator
project sessions. One of them received OSC messages from OSCHook as input, and
the other one received OSC messages from HandPosition-OSC.

![pd](https://github.com/seatabay/thereminator/assets/36015029/b5059661-c6c9-4667-aa02-a8fc375cd742)


**OSCHook as Input (Pitch Control)**

To train this model we made sure:
- Our IP Address on the OSCHook app is the same as the IP Address on which the
laptop is connected.
- The inputs and control port of the Wekinator is the same as the port through which
the OSCHook sends the Light Intensity measurements (port 6448 in our case).
- The Input OSC message is set as: /light
- The number of inputs is set to: 1
- The output port is set to 7008.
- The number of outputs is set to: 1
- The type of outputs is set to: All continuous
We started the measurement of light intensity on OSCHook app and placed the
phone horizontally on the desk. During the training, we consecutively increased the
output slider while moving the right hand away from the phone. For instance, 0
corresponds to hand on phone and 1 corresponds to the maximum distance from the
phone surface. The maximum distance from the phone surface, which can still be
tracked from the OSCHook app, differs according to the light intensity of environment.
Thus, it is advised to find the limit of the maximum distance and train the model
accordingly.

![acc](https://github.com/seatabay/thereminator/assets/36015029/919d8db8-248c-4ace-a505-1d19e2fa7c27)


**HandPose-OSC as Input (Amplitude Control)**

To train this model we made sure:
- The raw box on HandPose-OSC interface is checked.
On the Wekinator side:
- The input port is set to: 8008
- The number of inputs is set to: 63
- The Input OSC message is set as: /landmarks
- The output port is set to: 12000
- The number of outputs is set to: 1
- The type of outputs is set to: All continuous
For HandPose-OSC, the same training technique used for OSCHook is applied. The
closed hand corresponds to 0 and an open hand corresponds to 1, the maximum volume.
How to use the Thereminator:
- Open wekinator and open the two projects with the two trained models.
- Open HandPose-OSC and check the 'raw' box.
- Make sure your left hand is being detected by HandPose-OSC through your camera.
- Open OSCHook on your android smartphone and tick the send OSC messages for
Light Intensity. Set the port to 6448 and the IP address as the one you are connected on
your laptop.
- Open the pd patch and click on the toggle to turn DSP on.
- Run both wekinator projects.
- Use your right hand to control the frequency/pitch of the output sound by changing
the distance between your hand and the phone.
- Use your left hand to control the volume of the output sound, by closing your hand
into a fist to turn the volume down and open up gradually to increase the volume. For
full volume open your hand completely.
Note: The light intensity of the environment where the model is trained affects the
accuracy of the model. Therefore, it is advisible to keep the lighting during the
performance as similar as possible to the lighting during training.

![hand](https://github.com/seatabay/thereminator/assets/36015029/2261bf9e-0b8f-4dc4-945a-04e749542279)


**Future Improvements**
We aim to add another option for sound sythesis mechanism with Supercollider.
