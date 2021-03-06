# Some example / samples of working HB objects via HBJSON in Jupyter Notebook
## Will hopefully grow into 'turn HB model into multiple pandas dataframes for tabular review and also some kinda eppy/opyplus esque functionality'... becuase why not..
***
Big shout to EM: I leaned how to do this from him sooo I take 0 cred
***
the jist:
```python
import json
import honeybee.dictutil as hb_dict_util
import honeybee_energy.dictutil as energy_dict_util
import honeybee_radiance.dictutil as radiance_dict_util
from honeybee.face import Face
from honeybee.room import Room
from honeybee.aperture import Aperture
from honeybee_energy.material.opaque import EnergyMaterial, EnergyMaterialNoMass
from honeybee_energy.hvac import *

"""
Crediting EM
"""


def build_hb_objs(_path):
    """ A function to take in a hbjson and rebuild hb_objs
        into 'py hb_objs'.
    ----------------------------------------------------------
    Args:
        * _path: path to hbjson
    Returns:
        * hb_objs
    """
    with open(_path) as json_file:
        data = json.load(json_file)

    try:
        hb_objs = hb_dict_util.dict_to_object(
            data, False)  # re-serialize as a core object
        if hb_objs is None:
            # try to re-serialize it as an energy object
            hb_objs = energy_dict_util.dict_to_object(data, False)
            if hb_objs is None:
                # try to re-serialize it as a radiance object
                hb_objs = radiance_dict_util.dict_to_object(data, False)
    except ValueError:
        # no 'type' key; assume that its a group of objects
        hb_objs = []
        for hb_dict in data.values():
            hb_obj = hb_dict_util.dict_to_object(
                hb_dict, False)    # re-serialize as a core object
            if hb_obj is None:
                # try to re-serialize it as an energy object
                hb_obj = energy_dict_util.dict_to_object(hb_dict, False)
                if hb_obj is None:
                    # try to re-serialize it as a radiance object
                    hb_obj = radiance_dict_util.dict_to_object(hb_dict, False)
            hb_objs.append(hb_obj)
    return hb_objs

### This is where the fun begins:

my_ipynb_hb_obj = build_hb_objs('path//to//hbjson')

```
## And now:
>  my_ipynb_hb_obj is the same as any hbobj in Grasshopper ghpy!
