Resource Packs - Custom Items, Sounds, Font And More
----------------------------------------

This page will answer some common questions from programmers interested in creating visually custom items and adding new sounds into a resource pack.
There are plenty of tutorials on creating your own resource pack, and less commonly how to implement custom model data and manage sounds within one.
This guide primarily, details how to correctly format your resource pack, and then implement it into Minecraft using Denizen scripts.

```eval_rst
.. contents:: Table of Contents
    :local:
```

### Inside The Root Directory

> Directory: `.minecraft/resourcepacks/my_resource_pack/`

The main directory within your resource pack folder should contain both:
- The `Assets` Folder - This is where all your files are placed.
- `pack.mcmeta` - This is how Minecraft knows what format your Resource Pack is.
Optimally, you can also include `pack.png` - This is a `64x64` custom image for your pack!

#### Example File: `pack.mcmeta`

Inside the `pack.mcmeta` file, you will find this is formatted in a `json` format.
You need two named strings: `pack_format` and `description`.
Here is what it looks like inside:

```json
{
   "pack": {
      "pack_format": 5,
      "description": "My Fancy Resource Pack"
   }
}
```

##### pack.mcmeta File Key: `pack_format`

This is the indicator to Minecraft what version this pack is.
Pack formats cannot specify versions that exceed the version the client is on.
The client couldn't possibly guess what a newer format may be,
but can sometimes know how to use older formats.
- `1` indicates versions `1.6` - `1.8`.
- `2` indicates versions `1.9` - `1.10`,
- `3` indicates versions `1.11` - `1.12`,
- `4` indicates versions `1.13` - `1.14`,
- `5` indicates versions `1.15` - `1.16.1`,
- `6` indicates versions `1.16.2` - `1.16.4`,
- `7` presently indicates version `1.17`.

##### pack.mcmeta File Key: `description`

This can be blank, or you can optimally fill this with something fancy.
Unicode characters must be written pre-escaped, like this: `\uCODE`; two examples being:
 `\u2588` for `█`, and `\u00A7` for `§`, the section sign symbol which parses [valid color tags](https://minecraft.gamepedia.com/Formatting_codes) you use to parse colors in minecraft chat.
If you want red text, your text will look something like `\u00A74Dark Red!`.
*Note: Color before formatting; Formatting codes persist after a color code, Not vise-versa!*

You can find special characters in your Character Map.
If you're on a Windows operating system, `Start` > `Windows Accessories`.
If you're in Linux using GNOME and Unity, `Gucharmap character map` is a part of `GNOME desktop`.
If you run a Gnome desktop - you can access it in any of these following ways:
- Menu on the top of the screen, `(language)` > `Character Map`.
- `Gucharmap` in terminal.
- `Applications` > `Accessories` > `Character Map`.

You can also google search for unicode characters.

### Inside The Assets Folder Directory

> Directory: `.minecraft/resourcepacks/my_resource_pack/assets/`

This directory should be empty except for the one folder > Directory: `minecraft`. 
Toss it in and leave everything else out of here.

#### Advanced: Custom Namespace
Advanced users may utilize a namespace folder in this directory.
The only benefit to this feature for items and models is organization on a wider scale than just subfolders within subfolders.
Utilizing namespace specified directories for font prevents players from typing your custom image fonts by any reasonable means.
Examples of why you would want to prevent this: 
- [Black screen overlays, but on a sign](placeholder)
- [Black screen overlays, but in the chat](placeholder)
- [Fancy book images, but in the chat box](placeholder)
When creating overrides, you still must use the base item model in the `minecraft` namespace directory.
When pointing paths to files in your custom namespace, you must prefix it to every path that uses it.
Example model override pointing to a model saved at `assets/denizen_pack/item/custom/dserver_ubersword.json`:
```json
{ "predicate": { "custom_model_data": 1}, "model": "denizen_pack:item/custom/dserver_ubersword" }
```

### Inside The Minecraft Folder Directory

> Directory: `.minecraft/resourcepacks/my_resource_pack/assets/minecraft/`

Depending on what content you plan on changing, you can create any of the following folders:

- blockstates - This is where each block-state of materials are saved.
- font - This is where font data is saved. This guide does not cover this.
- models - This is where the model data and files are saved.
- textures - This is where the texture image files are saved.
- fonts - This is where your font data is saved.
- sounds - This is where your sounds are saved.
- optifine - This is where your optifine data is saved. This guide does not cover this.

For Optifine support, it's recommended you join their discord and review their documentation at their [Github Source](https://github.com/sp614x/optifine/tree/master/OptiFineDoc/doc).

\* *Note: Advanced users who opt using custom namespace directories also share these contents*

#### Example File: `sounds.json`

This file indexes where Minecraft should look for your sounds.
Below is an example of a setup for two custom sounds, `defence_levelup_0` and `defence_levelup_1`.

```json
{
  "entity.player.defence.level": {
    "sounds": [
      {
        "name": "custom/defence_levelup_0"
      },
      {
        "name": "custom/defence_levelup_1"
      }
    ],
    "subtitle": "Excited Trumpet Noises"
  }
}
```

The first key is the name for the sound; in this example, `entity.player.defence.level`.
The only data object within the command we need to specify are `sounds`.
Optionally, you can specify the subtitle that displays if subtitles are enabled in-game.
If you place multiple sounds within the `"sounds":[]` array, the sound will randomize between them based on their weight.
The file extension is `.ogg` - other formats are not compatible.

For each file, you will need the data: `"name":"FILEPATH/FILENAME"`, excluding the file's extension.
Optionally, you can manually adjust the following valid properties of the sound:

1) `volume` - The volume the sound will be played as. 
    - Default is `1.0`; Valid volume ranges from `0.0` to `1.0`; where `1.0` is the loudest it may be played at.
    - The Volume value accepts higher values using Denizen's PlaySound, however not by increasing the volume. It increases the audible distance the sound may be heard from. 
    - For example, volume 5 can be heard from five chunks away.
2) `pitch` - The pitch the sound plays at, altered from it's original `.ogg` form.
    - Default is `1.0`; Valid pitches range from `0.0` to `2.0`; where `1.0` is high-pitched and `0.0` will be low-pitched.
3) `weight` - The chance that this sound will be selected as opposed to randomly.
            - Higher integers are used more frequently.
4) `stream` - Determines if the sound should be streamed from it's file.
    - Default is false.
    - Recommended to set this value as `true` if the sound is longer than two seconds to avoid lag.
    - Use this sparingly; it's not optimal to specify everything true.
    - This is used with all music disks.
5) `preload` - Determines if the sound should be loaded when loading the pack, as opposed to when the player plays the sound.
    - Default is false.
    - Used for ambient noises.
6) `attenuation_distance` - Determines the reduction rate based on distance.
    - Default is `1.0`.
    - Used by portals, beacons, conduits.
7) `type` - determines if a pre-defined event fires this sound.
    - Default is `sound`, the other option available is `event`.
    - `sound` causes the value of `name` to be interpreted as the name of a file.
    - `event` causes the value of `name` to be interpreted as the name of an already defined event.
    - used for things like being under-water, in a cave, near a beacon, near a beehive.

### Inside The Blockstates Folder Directory

> Directory: `.minecraft/resourcepacks/my_resource_pack/assets/minecraft/blockstates/`

To modify each individual block-state of an item, you must specify each individual blockstate.
Additional blockstates cannot be specified.
When specifying blockstate models, the relative folder directs to the `Models` directory, located at `/assets/minecraft/models/`.
Adjusting these is not covered in this guide.

### Model File Structuring

It's important to know the structure of these files before entering the next two sections that guide you through creating and placing them within the pack.
To "Create" new items, you will need to modify existing items within Minecraft.
There are two model files you'll work with when creating items: The base item you are overriding and your newly modeled item.
Your base files are found within the `assets/minecraft/models/item/` directory.
These model files represent the item you override, and cannot be renamed as they are hardcoded into Minecraft.

#### Model File Property: `parent`

The parent key is important and you should not remove it unless you plan to replace the contents you remove by removing this property.
Previewing different item models within the Minecraft default resource pack, you will find a variety of different parent files.
For items, most use the parent `item/generated`, `item/handheld`, or `builtin/generated`.
These files, excluding those that utilize the `builtin` directory, are also located in the same directory.
The parent property is designed to minimize your model files by creating a basis for model files to follow suit(Hence the name "parent").
Most visible properties Minecraft uses for parent files are for the `display` property, however many properties are left hardcoded.

Advanced users can utilize the parent property to minimize the size of the resource pack.
If a series of custom modeled items share the exact same model but different textures,
you could for example place the model file's largest properties `elements` and `display` in a separate model file entirely,
and define the `textures` property in each item's model file individually.

#### Model File Property: `display`

This property instructs how the item's display should be shown to the player.
There are eight display positions:
- `thirdperson_righthand` - This is for third-person view of the item when held by entities.
- `thirdperson_lefthand` - This is for third-person view of the item when held by entities.
- `firstperson_righthand` - This is for first-person view of the item.
- `firstperson_lefthand` - This is for first-person view of the item.
- `gui` - This is for the item when in an inventory or in the player's hotbar.
- `head` - This is for the item when on an entity's head.
- `ground` -  This is for `dropped_item` entities displaying this item, or this item dropped in general.
- `fixed` - This is for the item when placed in an item_frame.

For each display position, you can specify three properties that change the display you see:
- `rotation` - Rotates the item around the three axis.
- `translation` - Specifies an offset from a center point that the item is moved to display at. Input can be anything from `-80` to `80`.
- `scale` - Specifies the scale of the model. Maximum upscale is `4`, default is `1`.

\* *Note: Translation is applied to the model before rotation.*

Here's an example of a valid display property:

```json
"head": {
    "rotation": [ 0, 180, 0 ],
    "translation": [ 0, 13, 7],
    "scale":[ 1, 1, 1]
},
```

If all three of the axes (such as the scale property above) are default or unchanged, you can remove them safely.

#### Model File Property: `elements`

The contents in this property are typically generated by your model software.
This property is also typically very large, and can be safely left to your software to generate without modification.
If you are only adding textures without custom models, this property can be left off.

#### Model File Property: `Textures`

The contents here indicate the path to find the textures used in this model file.
The key used here is used for the `"texture": "#key"` property in the elements property.

#### Model File Property: `gui_light`

This property is rarely documented, but is incredibly important to prevent unwanted texturing for items.
When creating custom items, you'll find that your item is slightly darker in-game than it was created to be.
This is because all items are presented a level of light to shade the items.
When left defaulted, the light is placed awkwardly in a way that makes your item look saturated.
Valid values are `front` and `side`.
Block models should typically use `side`, this allows blocks to have a nice shaded model.
Flat item models should typically use `front`, this allows items to be given a clear GUI visual.

### Inside The Models Folder Directory

> Directory: `.minecraft/resourcepacks/my_resource_pack/assets/minecraft/models/`

This can and was previously done with Durability, but optimally utilized with `custom_model_data` that was implemented in Minecraft 1.14.
The three object model types for model data are `Block States`,`Block Models`, and `Item Models`.
Block States and Block Model data are not covered in this guide.

#### Example File: `wooden_sword.json`

Base model files such as `wooden_sword` for example, should look like this:

```json
{
    "parent": "item/handheld",
    "textures": {
        "layer0": "item/wooden_sword"
    }
}
```

The above example is `wooden_sword.json`, which is located at `/assets/minecraft/models/item/wooden_sword.json`.
The `parent` key indicates the model data this file injects data for.
The data's value for parent specified are the `FILEPATH/FILENAME` from the `models` directory if specifying a model file,
and the `textures` directory if specifying a texture. 

In the above example, the `wooden_sword` utilizes the parent model located at: `/assets/minecraft/models/item/handheld.json`.
In the above example, the `wooden_sword` utilizes the texture image located at: `/assets/minecraft/textures/item/wooden_sword.png`.

Note that removing `parent` keys if you are not specifying all display properties of an item will return unexpected results.
The `wooden_sword`, for example, utilizes the parent file `/assets/minecraft/models/item/generated.json`;
which also utilizes a parent file at `/assets/minecraft/models/builtin/generated.json`.
If these files do not exist altered in the pack, they utilize the respective existing file within Minecraft's default resource.
To add the `custom_model_data` predicate, we specify this in the `Overrides` key.
Here is an example of the override, and the `custom_model_data` specified.

```json
{
    "parent": "item/handheld",
    "textures": {
        "layer0": "item/wooden_sword"
    },
    "overrides": [
        { "predicate": { "custom_model_data": 1}, "model": "item/custom/dserver_ubersword" }
    ]
}
```

\* *Note: Remember that objects and arrays are separated by commas.*
The above example extends the item `wooden_sword` to have an additional item model when the item in-game has the mechanism applied.
This file is located at: `/assets/minecraft/models/item/custom/dserver_ubersword.json`.
Valid `custom_model_data` entries are integers, <span class="parens">(including larger integers, as opposed to the durability predicate which is more limited)</span>.
An example of this file with multiple custom model data's specified looks like this:

```json
{
    "parent": "item/handheld",
    "textures": {
        "layer0": "item/wooden_sword"
    },
    "overrides": [
        { "predicate": { "custom_model_data": 1}, "model": "item/custom/dserver_ubersword" },
        { "predicate": { "custom_model_data": 2}, "model": "item/custom/dserver_prosword" },
        { "predicate": { "custom_model_data": 3}, "model": "item/custom/dserver_greatsword" },
        { "predicate": { "custom_model_data": 4}, "model": "item/custom/dserver_decentsword" }
    ]
}
```

#### Example File: `custom_item.json`

Your custom item's model data file is something you may or may not adjust yourself.
There are plenty of options for modeling software available, two of which most commonly recommended are [Cubik Pro](https://cubik.studio/) and [BlockBench](https://blockbench.net/).
Note that the software you use must be able to export the model to a `.json` file format.
Cubik Pro specifically saves the model, and the respective image file, into it's correct locations and formats the model file correctly.
When you place your custom item's model data into the location you direct it to in the above example, the top of your model file should look something like this:

```json
{
	"textures": {
		"particle": "item/custom/handheld/dserver_ubersword",
		"texture": "item/custom/handheld/dserver_ubersword"
	},
```

Note that you do still need any other parts of the JSON file, such as the `"parent"` key.

In the above example the `particle` and `texture` keys both point to the image files we will be saving at the > Directory: `/assets/minecraft/textures/item/custom/handheld/dserver_ubersword.png`.

### Inside The Textures folder Directory

> Directory: `.minecraft/resourcepacks/my_resource_pack/assets/minecraft/textures/`.

This is where your image files are saved.
These files should be in the relative filepath specified within the model file that it corresponds to.

### Inside The Sounds folder Directory

> Directory: `.minecraft/resourcepacks/my_resource_pack/assets/minecraft/sounds/`.

The sound format Minecraft uses is `.ogg`.
Free converting tools can be found online, one recommended option being [Audio-Online-Convert.com.](https://audio.online-convert.com/convert-to-ogg).
For organization's sake, if you're adding new sounds, it is recommended that you place them in a folder named `Custom`. Minecraft's default resource organizes it's sounds by [category](https://hub.spigotmc.org/javadocs/bukkit/org/bukkit/SoundCategory.html).
You can find Minecraft's default resource sound index here: `/.minecraft/assets/indexes/1.16.json`; where `1.16` is the version we're using in this guide.
All of your sound files <span class="parens">('.ogg' files)</span> should be saved in this directory.

### Inside The Font Folder Directory

> Directory: `.minecraft/resourcepacks/my_resource_pack/assets/minecraft/fonts/`.

Minecraft default uses the `default.json` file as the primary font used.
Within this file is a root property named `providers`.
Inside the providers property, we can specify three different types of font providers:
- `bitmap` - Used to display images as text.
- `legacy_unicode` - Deprecated and old don't use it.
- `ttf` - Used to insert font styles using `.otf` and `.ttf` font files that you can download and import.

The two main font providers have different pairs of properties available.
For `bitmap` provider types:
- `file` - This is the path to the image this font character will use, starting at `/assets/minecraft/textures`.
- `height` - Optional property to adjust the height of the image. Can be negative, but the maximum is 256 minus the `acsent` specified, if any.
- `ascent` - The vertical shift to the font image.
- `chars` -  list of strings containing the characters used for an image map. The texture is split into one equally sized row for each element of this list. Each row is split into one equally sized character for each character within one list element.

Additionally, image font providers are limited to `256`x`256` in size.

#### Example File: `my_fancy_font.json`

Here's an example of a correctly formatted font file:

```json
{
    "providers": [
        {
            "type": "ttf",
            "file": "negative_spaces.ttf",
            "shift": [0.0, 0.0],
            "size": 10.0,
            "oversample": 1.0
        },
        {
            "type": "bitmap",
            "file": "logos/icon/letter_large.png",
            "ascent": 0,
            "height": 200,
            "chars": [
                "\u6900"
            ]
        }
    ]
}
```

This utilizes a custom font named `negative_spaces`, and adds an image to the unicode character `6900`.
It's recommended when implementing large image font to use a file that is **not** the default.
Doing so prevents regular players from typing 256x256 images in the chat, easily griefing other players.

### Putting It Together: Using Denizen With Your New Pack

#### Custom Items

Giving yourself the item is simple. If it's a one-off time you need the thing or you're just generally testing, 
you can use the [`/ex` command](/guides/first-steps/ex-command) like this:
`/ex give wooden_sword[custom_model_data=1]`.
The item script simply looks something like this:

```dscript_green
UberSword:
    type: item
    material: wooden_sword
    mechanisms:
        custom_model_data: 1
```

The `custom_model_data` is in-line with any other mechanisms you choose to specify with the custom item.
You can give yourself the custom item just like any other item script, `/ex give dserver_ubersword` or in any script with the `give` or `inventory` command.

#### Custom Sounds

Playing your sound is relative to the unique custom name you gave it.
In our example, we specified the name of the sound as `entity.player.defence.level`.
You can play this sound with the `playsound` command like this: `/ex playsound <player> entity.player.defence.level custom`.
In a script, this would look something like this:

```dscript_green
MyCustomSound:
    type: task
    script:
        - playsound <player> sound:entity.player.defence.level custom
```

#### Custom Font

Custom font has a variety of different uses. Font that utilizes images can be displayed anywhere text can be displayed which includes:
- books
- signs
- bossbars
- the sidebar
- entity names
- actionbar messages
- the player tablist
- title and subtitles
- item display names and item lore
- the chatbox and chat hover event displays

Just look at the possibilities:

![image](placeholder)

The only things you need to implement thse are these tags:
- `<&chr[<character>]>` - where `<character>` is your unicode character used
- `<ElementTag.font[<font>]>` - where `<font>` is your font file name
- `<&font[<font>]>` - where `<font>` is your font file name

The `<font>` input is a path starting from `assets/minecraft/font/`;
Advanced users opting to use a namespace use `namespace:<font>`, which would use the path starting from `assets/namespace/font/`

### Troubleshooting

For most problems you encounter when creating your resource pack, basic users encounter a small number of problems.
Here's a list of common issues, followed by good solution troubleshooting methods to resolve them.

#### Item Problems: Nothing Changed Or Purple And Black Squares

For all problems in this section, verify the following:
- Verify your item's entire path is lowercase for folders and file names.
- Verify your folder naming schemes - It's common to accidentally mix between different naming schemes using `items` instead of `item`, `model` instead of `model`, or `blocks` instead of `block`.
- Verify your models use valid json, and are the correct file extensions. YAML is not valid JSON.
- Verify your file path matches the correct folders used.

If you've given your item the custom model data specified for your model and nothing happened, your resource pack is not registering any changes made to the item. 
This is most commonly caused by assuming you can just dump model files blindly into the resource pack without using proper names.
- Verify the original minecraft model file for the item you are overriding is in your resource pack and points to your model file as an override.
- Verify your resource pack is not nested within another folder. The directory should start like this: `.minecraft/resourcepacks/my_resource_pack/assets/`

If your item is displaying purple and black squares, you've just received Minecraft's missing texture. 
Your problem is much easier than the previous problem to troubleshoot.
If your item is flat with purple and black squares, your item's model is missing or misconfigured.
- Most likely: Verify your base item's path correctly points to your new custom model file.

If your item is a correctly modeled item but textured with purple and black squares, your item's textures are missing or misconfigured.
This is most commonly caused by model software pre-generating your model file's texture pack incorrectly for your pack.
- Most likely: Verify your model file's path correctly points to your new custom model's textures used.
- Verify you did not include the file extension in the path.
- Verify your `textures` property in the model file correctly matches the `texture` key in the elements property. Typically this shouldn't be touched to begin with unless you're proficiently replacing the text across the entire model file when doing so.

Very rare problems:
- If your texture contains an alpha layer / transparency but displays black instead, this item does not support transparent textures. Use a different item.
- Compass and Clocks have multiple textures and models to override.

#### Font Problems: Invisible Text Or Clear Squares

placeholder

#### Advanced Troubleshooting

If the above all fail, ultimately you can resort to a verbose method of troubleshooting by checking your client logs.
These are located by default in the `.minecraft/logs/` directory, labeled `latest.log`.
If you use standard clients such as the vanilla client or Optifine, these logs do not contain personal information if you choose to share it with others.

### Tips, Tricks, And Notes While You Create

A very handy trial-and-error debugging tricks for creating resource packs is that you can actively edit the pack and view your changes in-game.
One of the most common misconceptions of resource packs is that you need to have it saved as a `.zip`.
FALSE! You can save this directly in your resource packs folder, edit and just reload!
The default hotkey to reload your resource packs is `F3 + T`.

There is an incredibly handy JSON formatter and Validator you can find [Here](https://jsonformatter.curiousconcept.com/) for checking your JSON data.
Minecraft will give no indicators excluding broken texture images and models if your files are wrongly formatted.

Custom textures, models and sounds can be placed within as many sub-folders as you would like. Remember to abide the lowercase sensitivity.

Your default Resource Packs folder is located in your default minecraft directory, and looks something like this:
`C:/Users/username/AppData/Roaming/.minecraft/resourcepacks` on Windows, or `/home/[username]/.minecraft` on Linux.
Optimally, you can directly open the folder directory with the `Open Resource Pack Folder` button in the `Resource Packs...` section of your in-game menu.

The best template for modifying existing models and textures for Minecraft is the default resource,
which can be found in your Version Jar directly located in the > Directory: `/.minecraft/versions/`.
You can extract this to it's respective file and locate the `Assets` folder within.
Note that if you copy the entire `assets` folder as a template, you may consider removing material you don't change,
as it's extra file storage you don't need to contribute to the resource pack.

### Related Technical Docs And Links

* [Playsound Command Meta](https://one.denizenscript.com/denizen/cmds/playsound)
* [JSON Formatter and Validator](https://jsonformatter.curiousconcept.com/)
* [Online-Convert.com | Convert Audio to OGG Format](https://audio.online-convert.com/convert-to-ogg)
