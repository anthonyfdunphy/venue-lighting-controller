# Touchdesigner Lighting Controller
Touchdesigner Lighting Controller for a venue. Basic dimmer controller. Created using Custom Class in TD with methods

### Features
There are 12 buttons on the GUI which are clickable. When each button is clicked they run a function which sets the lighting intensity based on the button and assigns it to the corresponding dimmer channel. 

Example: Button 3 controls dimmer channel 3.

All of the button are UI elements are the panel state (i.e. On or Off) is a numeric value of 0 and 1. I merged all the buttons together with a Merge Chop. I then use a function called extractChannel which takes the channel name as a variable and regex pattern match to determine the current dimmer channel value. When button 4 is pressed on from the off position, the extracted channel name is stored in a variable called 'chanVal' and is used to set the dimmer to on with the 'SetDimmerOn' class method from the 'lighting' component. This is shown in the code below.

```
# me - this DAT
# 
# channel - the Channel object which has changed
# sampleIndex - the index of the changed sample
# val - the numeric value of the changed sample
# prev - the previous sample value
# 
# Make sure the corresponding toggle is enabled in the CHOP Execute DAT.
import re

def extractChannel(channel):
    match = re.search(r'\d+', channel)
    if match:
        return match.group(0)
    else:
        return None


def onOffToOn(channel, sampleIndex, val, prev):
	chanVal = extractChannel(channel.name)
	op('lighting').SetDimmerOn(int(chanVal))
	return

def whileOn(channel, sampleIndex, val, prev):
	return

def onOnToOff(channel, sampleIndex, val, prev):
	chanVal = extractChannel(channel.name)
	op('lighting').SetDimmerOff(int(chanVal))
	return

def whileOff(channel, sampleIndex, val, prev):
	return

def onValueChange(channel, sampleIndex, val, prev):
	return
	
```

### Future Features
There are some features I wish to add to this controller in the future. One being, the ability to create a 'scene' by turning on the desired dimmer channels. When this is done, the user selects a button called 'save scene' this will save the channel value in an array or list which can be called back at any time through the GUI front end.

This should save the active scene values by name and value to a new list with Python. When the scene is called, then the scene is called using Python.

```
Example code would be something like this:

n = op('merge_buttons')
my_dict = {c.name: c for c in n.chans()}
for name, value in my_dict.items():
    print(name, value)

```


I have created a section of code that runs when the "Save" and "Cue" button is hit:
```
lightingScene = op("active_scene")
savedScene = op("saved_scenes")
numDimmers = lightingScene.numChans

scenes = {}
savedScenes = {}

def onOffToOn(channel, sampleIndex, val, prev):

	global scenes, savedScenes

	if channel.name == "save":
		for d in range(numDimmers):
			#getting channel names from Constant CHOP and setting to key
			key = getattr(lightingScene.chan(d), 'name')
			value = lightingScene[d].eval()
			scenes[key] = value

	else:
		# Iterate through the keys and access their values
		for i, scene in enumerate(scenes):
			setattr(savedScene.par, 'value{}'.format(i),scenes[scene])

	return
```

