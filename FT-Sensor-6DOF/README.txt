This is an attempt to implement a 6DOF Force-torque sensor (like you can see on robotic arms for delicate handling or teaching).
As you might notice if you were interested in such a sensor, the prices are exorbitant ($2500 for the cheapest model, $5000 and up for the usual models), which is totally out of reach for a normal hobbyist.
Those have a very delicate internal structure which has been described in many scientific articles: 3 or 4 spokes measuring twist mounted on 3 or 4 pads measuring linear movement. Then an algorithm takes in all their data and integrates into FX, FY, FZ forces and MX, MY, MZ torques, and then corrects them for precise calibration.

Sounds hard, however, the concept seems to be quite understandable and I think can be done on a hobby budget - and with a hobby level of load capacity and precision! - with simple parts. 
What a cool addition to a robot arm this would be, if only anyone with a robot arm could build it! Also the thing can be used as a 6-DOF input device for 3D modeling, I have seen it in some advanced flight simulators under the name of a "Hard stick", and some wheelchairs as "chin control".

My concept is based on the idea of using a PCB as a flexible base for mounting a strain gauge (the same device as in the "normal" load cell or used for measuring PCB strain during handling) here:
https://curiousscientist.tech/blog/pcb-weighing-scale-ver2
And a vresion developed commercially here:
https://www.hoffmann-krippner.com/polymer-strain-gage/
Or in an extreme version using peculiar physical properties of conventional SMD resistors here: 
https://github.com/IvDm/Z-probe-on-smd-resistors-2512

Why can't we apply the same concept in two dimensions and for measuring three directions of applied force? I think we can!
Even though technically we need just 3 spokes to extract all parameters that we need, but this does not nicely translate into usable X, Y, Z axes, and that would require corrections by sin/cos of 30/60/120 degrees before we even start calculations. I prefer to get something working first and move towards a minimal solution later.
I was trying to mimic the design on page 6 of: Design, Manufacture, Test and Experiment of Six-Axis Force Torque Sensor for Chinese Experimental Module Manipulator
I decided to add  temperature sensors on outer load cells to compensate self-heat (first calibrate with known weight and changing temp), as described in: Load Cell Correction Squeezing every last bit of accuracy out of your load cells

Without further ado, we will need: 
8 sets of 4 strain gauges: 4 for each wheatstone bridge, 4 sets for horizontally mounted bridges, 4 sets of vertically mounted bridges.
To learn more about why we need wheatstone bridges, I refer you to Sparkfun's tutorial: https://learn.sparkfun.com/tutorials/getting-started-with-load-cells

4 HX711 load cell amplifiers. We will use both of their 2 channels (remember that has some special implications!). We could use the ready modules, but those would be very bulky and not fit the PCB nicely. I must mention that Sparkfun's modules are the best due to the use of some components to reduce noise - a 3.3 uH inductor and capacitors wherever needed to reduce noise.

Blue Pill board: has everything we need to acquire the data from the four HX711's, read analog temperature sensors, integrate all that into something usable and put out through Serial.

4 10K NTC thermistors: mounted near the strain gauges and can be used to correct the readings for temperature. There is research showing strain gauge temperature drift. I'm not sure if that applies to wheatstone bridges too. While our resulting precision will be questionable anyway, let's at least give ourselves a chance to increase precision if we achieve some meaningful readings in the first place.

Physical construction: 
Good FT sensors are built of precision CNC machined components. That is too expensive, so we will cut corners.
We will use cheap PCB prototyping services and their generous discounts to do all the hard work for us, and all our machined areas can be board cutouts.
We'll employ the principle of assembling PCBs the same way as slot-together laser parts are assembled, just sliding the spokes into the slots in the base, and then inserting the center ring and rotating it in place. 
To hold the slot-in parts firmly we will solder them to the exposed copper pads while holding the assembly together with a 3D printed temporary holder (TBD!). This soldering is purely structural, and let's hope the bonding between the board and copper will be strong enough to hold this together.
Once the structure assembly is done, the strain gauges can be glued in the areas marked on the silkscreen with a grop of superglue (preferably one that is intended for gluing flexible items), which will spread itself under the surface of the strain gauge. The thinner is the glue and the closer the entire strain gauge is to the board, the better the precision of the assembly will be. 
This is probably the most crucial part of the assembly that will define the precision and calibration of the resulting device. Remember: strain gauges only measure elongation in one direction, so they must always be mounted along the path of the strain, as marked on the board.
Then, the center ring can be bolted to whatever adapter you have for the endpiece.
Then, the base board must be mounted on all mounting holes to a solid base. It is important to use the 8 bolts near strain gauges in order to have the board bend in the correct places.
The DAQ (CPU) board may be mounted directly below the base board (allowing enough space for the CPU and spokes), or separately and connected with four IPC 6-pin cables. In the latter scenario, we are introducing more failure points: temp sensor readings may pick up more noise and the supply voltage may drop across the cable, but the data lines between the CPU and HX711 should be fine.

The assembly CAD can be seen in FT_Sens_6DOF STEP file. A soldering fixture needs to be designed based on this file.

