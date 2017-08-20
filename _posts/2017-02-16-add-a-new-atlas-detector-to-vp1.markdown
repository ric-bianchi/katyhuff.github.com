---
layout: post
title: Add a new ATLAS sub-detector to VP1
date: '2017-02-16 10:26:17'
tags:
- data-visualization
- atlas
- vp1
---

Here below we will see how to add a new piece of detector to VP1, to get it rendered and displayed correctly.

As an example, let us say we want to add to VP1 the visualization of the new AFP (ATLAS Forward Proton) detector.

### Step 1 - VP1Algs

**We start by setting a command-line flag in `VP1Algs/share/vp1`.**

We set a new variable towards the beginning of the file: `FLAG_AFP=0`
We catch the command-line arg:

    elif [ "x${arg}" == "x-afp" ]; then
    FLAG_ALFA=1
        
We add an entry in the VP1 usage message:

```bash
echo "-afp : Init geometry and converters for the AFP forward detector."
```

If the new command-line option is used, then I set a variable to be passed to the python script (see next step):

```language-bash
if [ "x$FLAG_AFP" != "x0" ]; then
if [ "x$OPTS" != "x" ]; then OPTS="$OPTS;"; fi
OPTS="${OPTS}vp1AFP=True"
fi
```

**Now we edit the python launcher `VP1Algs/share/vp1.py` to load the new geometry from Athena.**

Towards the beginning of the file, I initialize the new variable to zero by default:

```python
if not 'vp1AFP' in dir(): vp1AFP=False
```

Then, I set the new variable to ON, if it has been passed by the `vp1` bash script (from step 1):

```language-bash
if (vp1AFP): DetFlags.AFP_setOn()
else:        DetFlags.AFP_setOff()
```

*Note:* `DetFlags` is an Athena python module, which defines the flags for all the ATLAS sub-detectors; [you can find it here](http://acode-browser2.usatlas.bnl.gov/lxr-rel20/source/atlas/Control/AthenaCommon/python/DetFlags.py).


#### Step 2 - VP1GeometrySystems


**In the file `VP1GeometrySystems/VP1GeometrySystems/VP1GeoFlags.h`, define a new *bit flag* for the new sub-detector.**

to be written

*Note:* in C++ an enum is handled as an int value; so you can safely define up to 32 flags with hex strings.

**Add the new system to the GUI controller.**

In the file `VP1GeometrySystems/src/VP1GeometrySystem.cxx`:

1. add the new detector to the function `VP1GeometrySystem::buildController()`:

        
        d->addSubSystem( VP1GeoFlags::AFP,"*AFP*");
        
   
2. add a new `case` to the method `VP1GeometrySystem::Imp::createPathExtras`:

        
        case VP1GeoFlags::AFP:
        
2. In the file ``, create a new entry for the new piece of detector:

        Imp::defineSubSystem(VP1GeoFlags::AFP,"AFP",Imp::MISC);

    this will appear in the tree within the "Browser" window of the "Geo" tab:
    
    ![](/content/images/2017/02/Screenshot-Browser--Geo-.png)

3. Using the "Qt Designer" program (once the ATLAS environment has been set, you can launch this typing `designer` at the command-line), open the file `VP1GeometrySystems/src/geometrysystemcontroller.ui` and add to the UI a checkbox for the new detector and call it, for example, `checkBox_ALFA`:

    ![](/content/images/2017/02/Screenshot-Qt-Designer.png)

4. In the `VP1GeometrySystems/src/GeoSysController.cxx` file, map the new checkbox to the corresponding GeoFlag adding this line:

        d->subSysCheckBoxMap[VP1GeoFlags::AFP] = d->ui.checkBox_AFP;


----

Now, start VP1 with the new flag: `vp1 -afp`; you will see the "AFP" checkbox enabled. If you check it, the AFP geometry will be displayed.

![](/content/images/2017/02/afp_1.png)

