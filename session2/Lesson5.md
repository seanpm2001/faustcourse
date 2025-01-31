
## Lesson 5: UI Primitives

- buttons and sliders
- bargraphs
- groups
- attach

In this lesson we are going to see how user interfaces can be described in Faust. We have already seen some of the user interface elements in lesson 3. But there are additional ones like bargraphs that are used to display values, or numerical entries. Moreover, all these elements can be arranged vertically or horizontally using appropriate grouping schemes. Widgets and groups have a name. These names can contain additional information, called metadata, to customize their appearance or the way they are controlled.

Please note that this user interface description is an _abstract description_: the actual appearance of the UI elements and,
the toolkit used for that, are not defined in the Faust code. The appearance of the user interface will be defined later by
the architecture file.

In other words, there is a separation of concerns between the _abstract description_ of the user interface and its _actual
implementation_. The advantage of this approach is that a same abstract description can be implemented in different manners.
For example in many architectures we have at the same time, a graphical implementation, an OSC (Open Sound Control)
implementation, a MIDI implementation and, some times even an HTTP implementation, allowing the application to be remotely controlled.


[SLIDE 32: UI widgets]
On this slide you can see various kind of widgets. On the left side you have a button, a checkbox, and a numerical entry. On the center you can see a vertical slider. And on the right,you can find an horizontal slider, and horizontal bargraph and a vertical slider with a knob style.

### Buttons and checkbox
As you can see, buttons and checkboxes have only one parameter: a label. By default, a button generates a signal that is 0. It rises to 1 when the user presses the button and goes back to 0 when it releases it.

Vertical and Horizontal sliders have five parameters: a label, a default value (the value of the widget when the program starts), a minimum value, a maximum value and a step value. The signal produced reflects the action of the user on the thumb. Depending on the architecture, the step parameter might be used to quantize the produced values.

Numerical entries are a more compact alternative to sliders and have the same five parameters.

A Bargraph has only three parameters: a label, a minimum value and a maximum value. The role of a bargraph is to display the instantaneous value of the incoming signal clipped between the minimum and the maximum value.

### Groups
[SLIDE 33: Horiz and Vert groups]

Vertical, Horizontal and Tab groups provide a way to layout the widgets of a User Interface. The slide shows an example of a very simple user interface for a 8 channels mixer. The top level group is a horizontal layout. Inside this group we have eight input channels, two vertical bargraphs and master control. Each channel has a level slider and a panning control.

### Attaching bargraphs
[SLIDE A FAIRE]
The `attach` primitive takes two input signals and produce as output signal the first input signal. The role of attach is to force the second input signal to be compiled with the first one. From a mathematical point of view `attach(x,y)` is equivalent to `1*x+0*y`, which is in turn equivalent to `x`, but it tells the compiler not to optimize-out `y`.

To illustrate the role of `attach`, let say that we want to develop a mixer application with a vumeter for each input signals. Such vumeters can be easily coded in Faust using an envelop detector connected to a bargraph. The problem is that these envelop signals have no role in the output signals. Using `attach(x,vumeter(x))` one can tell the compiler that when x is compiled `vumeter(x)` should also be compiled.

[**demo**]

    import("stdfaust.lib");

    meter   = _ <: _, display : attach
  			with {
  				envelop = abs : min(1.00) : max ~ -(1.0/ma.SR);
  				display = envelop : hbargraph("meter", 0, 1);
			};

    process = os.osc(440) : _ * hslider("level", 0, 0, 1, 0.001) : meter;


[SLIDE 34: UI recap]

Let's recap this lesson on User Interface elements.

Button, checkbox, vslider, hslider and nentry are all signal generators that translate user actions into signals. The appearance of a slider can be transformed into a knob by inserting the metadata "...[style:knob]...".
vbargraph and hbargraph are used to display the incoming signal.
Sometimes we don't want to use their output signal, we then have to attach it to a signal that we are really going to use.
hgroup, vgroup and tgroup are used to layout the user interface.



