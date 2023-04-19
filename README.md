# Touchdesigner Lighting Controller
<img alt="header_image" width="100%" src="https://i.imgur.com/j4EsrXK.png" />
Touchdesigner Lighting Controller created for a venue - a powerful yet user-friendly solution for controlling lighting at any venue. This project has been developed with the goal of providing a reliable, easy-to-use lighting controller that doesn't require extensive training or experience to operate. The purpose of developing this interface was to familarise myself further with more advanced scripting and python principles such as dictionaries, list etc. With 12 slider components, a "cue" and "save" button, and a clickable list of saved scenes, this rudimentary lighting controller allows you to quickly and easily create stunning lighting effects that will captivate your audience. Whether you're hosting a concert, a theatrical performance, or a corporate event, this lighting controller is the perfect tool for adding an extra layer of excitement and professionalism to your production.

### User Flow

The user flow and code logic is outlined in these following steps:

1. User presses the "Save" button in the UI.
2. A dialog box pops up asking the user to enter a name for the scene.
3. The user enters the name in the dialog box and clicks "OK".
4. The current lighting level values are saved as a new key-value pair and are added to a dictionary of dictionaries called "scenes", where the key is the name of the scene entered by the user, and the value is the "scenes" dictionary.
5. The "scenes" dictionary is stored in a TDU dependency object called "project_data".
6. The UI updates to display the new scene in a list of available scenes.
7. The user can select a previously saved scene from the list and click a button to recall the lighting values for that scene.
8. The recalled lighting values are applied to the lighting fixtures.
9. If the user wants to add more scenes, they can repeat the process from step 1.

#### Code

```
lightingScene = op("active_scene")
numDimmers = lightingScene.numChans

def onOffToOn(panelValue):
	return

def whileOn(panelValue):
	return

def onOnToOff(panelValue):
	scenes = {}
	
	# when the ok button is selected, the current name
	# of the scene is added to the scene table
	for d in range(numDimmers):
		# getting channel names from Constant CHOP and setting to key
		key = getattr(lightingScene.chan(d), 'name')
		value = lightingScene[d].eval()
		scenes[key] = value
		
	#setting the name entered in the button to the key value of the dictionary
	op("project_data").ShowScenes.val.update({str(op('popDialog/entry/inputText').par.text): scenes})
	
	#this .modified() function is called to notify the dependent operators 
	op("project_data").ShowScenes.modified()
	
	return
```

#### Touchdesigner Session Organisation
<img alt="header_image" width="100%" src="https://i.imgur.com/l2rBTeS.png" />

### Features
There are 12 sliders which show on the UI, there is a radio toggle group of two buttons to allow the user to select between fader control and cue list control. When the user selects the "save" button, a pop button opens and the user is asked to name the lighting scene. This scene is a snapshot of the currentl lighting levels and can be call by simply clicking on the scene name in the cue list. 

Example: Button 3 controls dimmer channel 3.

Scene Naming            |  Fader Control/Cuelist Control
:-------------------------:|:-------------------------:
![](https://i.imgur.com/tBoudqh.png)  |  ![](https://i.imgur.com/SIsDvEx.png)

All of the button are UI elements are the panel state (i.e. On or Off) is a numeric value of 0 and 1. I merged all the buttons together with a Merge Chop. I then use a function called extractChannel which takes the channel name as a variable and regex pattern match to determine the current dimmer channel value. When button 4 is pressed on from the off position, the extracted channel name is stored in a variable called 'chanVal' and is used to set the dimmer to on with the 'SetDimmerOn' class method from the 'lighting' component. This is shown in the code below.


### Future Features
I would like to add a "Cue Button" that would grab the current value of the scene in the "cue list" and then increment to the next scene in the list. Once the value of increment exceeds the length of the dictionary value, then the cue button can no longer increment in value. I would also like to create a "delete button" which accesses the dictionary and deletes that from storage while also updating the cue list scenes.
