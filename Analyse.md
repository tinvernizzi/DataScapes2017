

```python
import pandas as pd
dfTravel = pd.read_csv('./dfTravelDescribe.csv', encoding = 'latin', header=0)
dfTravel.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>LinkId</th>
      <th>Speed_kmh_count</th>
      <th>Speed_kmh_mean</th>
      <th>Speed_kmh_std</th>
      <th>Speed_kmh_min</th>
      <th>Speed_kmh_25</th>
      <th>Speed_kmh_50</th>
      <th>Speed_kmh_75</th>
      <th>Speed_kmh_max</th>
      <th>Median</th>
      <th>SrcLatitude</th>
      <th>SrcLongitude</th>
      <th>DestLatitude</th>
      <th>DestLongitude</th>
      <th>LatitudeMean</th>
      <th>LongitudeMean</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>LAy_65-67</td>
      <td>78113.0</td>
      <td>9.826105</td>
      <td>10.848613</td>
      <td>0.376000</td>
      <td>2.383099</td>
      <td>6.042857</td>
      <td>12.533333</td>
      <td>56.400000</td>
      <td>6.042857</td>
      <td>45.505217</td>
      <td>-73.570633</td>
      <td>45.506133</td>
      <td>-73.572633</td>
      <td>45.505675</td>
      <td>-73.571633</td>
    </tr>
    <tr>
      <th>1</th>
      <td>LAy_66-65</td>
      <td>102680.0</td>
      <td>13.641882</td>
      <td>11.946840</td>
      <td>0.375995</td>
      <td>4.254545</td>
      <td>10.173913</td>
      <td>20.057143</td>
      <td>59.236364</td>
      <td>10.173913</td>
      <td>45.504283</td>
      <td>-73.568733</td>
      <td>45.505217</td>
      <td>-73.570633</td>
      <td>45.504750</td>
      <td>-73.569683</td>
    </tr>
    <tr>
      <th>2</th>
      <td>LBH_80-66</td>
      <td>37174.0</td>
      <td>10.336483</td>
      <td>10.301811</td>
      <td>0.462000</td>
      <td>2.053333</td>
      <td>6.872727</td>
      <td>15.120000</td>
      <td>59.400000</td>
      <td>6.872727</td>
      <td>45.503033</td>
      <td>-73.566383</td>
      <td>45.504283</td>
      <td>-73.568733</td>
      <td>45.503658</td>
      <td>-73.567558</td>
    </tr>
    <tr>
      <th>3</th>
      <td>LBe_85-86</td>
      <td>116020.0</td>
      <td>21.484541</td>
      <td>16.055218</td>
      <td>0.682000</td>
      <td>8.129801</td>
      <td>19.181250</td>
      <td>32.305263</td>
      <td>72.211765</td>
      <td>19.181250</td>
      <td>45.515317</td>
      <td>-73.561083</td>
      <td>45.517033</td>
      <td>-73.564717</td>
      <td>45.516175</td>
      <td>-73.562900</td>
    </tr>
    <tr>
      <th>4</th>
      <td>LBe_86-85</td>
      <td>50987.0</td>
      <td>12.040391</td>
      <td>11.857250</td>
      <td>0.682000</td>
      <td>2.316226</td>
      <td>8.584615</td>
      <td>18.052941</td>
      <td>72.211765</td>
      <td>8.584615</td>
      <td>45.517033</td>
      <td>-73.564717</td>
      <td>45.515317</td>
      <td>-73.561083</td>
      <td>45.516175</td>
      <td>-73.562900</td>
    </tr>
  </tbody>
</table>
</div>




```python
import gmaps
import gmaps.datasets
gmaps.configure(api_key="AIzaSyAYUik8KeTajhDGqevqUXTeaOX1momjqWI")

fig = gmaps.figure()
heatmap_layer = gmaps.heatmap_layer(
    dfTravel[["LatitudeMean", "LongitudeMean"]], weights=dfTravel["Speed_kmh_mean"],
    max_intensity=50, point_radius=0.0015, dissipating=False
)
fig.add_layer(heatmap_layer)
fig
```


<p>Failed to display Jupyter Widget of type <code>Figure</code>.</p>
<p>
  If you're reading this message in Jupyter Notebook or JupyterLab, it may mean
  that the widgets JavaScript is still loading. If this message persists, it
  likely means that the widgets JavaScript library is either not installed or
  not enabled. See the <a href="https://ipywidgets.readthedocs.io/en/stable/user_install.html">Jupyter
  Widgets Documentation</a> for setup instructions.
</p>
<p>
  If you're reading this message in another notebook frontend (for example, a static
  rendering on GitHub or <a href="https://nbviewer.jupyter.org/">NBViewer</a>),
  it may mean that your frontend doesn't currently support widgets.
</p>




```python
from matplotlib.cm import binary
from matplotlib.colors import to_hex
import matplotlib as mpl
             
def calculate_color(val, maxi, mini):
    """
    Convert the GINI coefficient to a color
    """
            
    # make gini a number between 0 and 1
    normalized_gini = (maxi - val) / (maxi - mini)

    # invert gini so that high inequality gives dark color
    inverse_gini = 1.0 - normalized_gini

    # transform the gini coefficient to a matplotlib color
    mpl_color = binary(inverse_gini)

    # transform from a matplotlib color to a valid CSS color
    gmaps_color = to_hex(mpl_color, keep_alpha=False)

    return gmaps_color

def display_map(arg):
    a = dfTravel[arg]
    a = [calculate_color(x, dfTravel[arg].max(), dfTravel[arg].min()) for x in a]

    fig = gmaps.figure()

    layer = gmaps.symbol_layer(
            dfTravel[["LatitudeMean", "LongitudeMean"]],
        fill_color=a,
        stroke_color=a,
    scale=5)

    fig.add_layer(layer)
    return fig

display_map("Speed_kmh_mean")
```


<p>Failed to display Jupyter Widget of type <code>Figure</code>.</p>
<p>
  If you're reading this message in Jupyter Notebook or JupyterLab, it may mean
  that the widgets JavaScript is still loading. If this message persists, it
  likely means that the widgets JavaScript library is either not installed or
  not enabled. See the <a href="https://ipywidgets.readthedocs.io/en/stable/user_install.html">Jupyter
  Widgets Documentation</a> for setup instructions.
</p>
<p>
  If you're reading this message in another notebook frontend (for example, a static
  rendering on GitHub or <a href="https://nbviewer.jupyter.org/">NBViewer</a>),
  it may mean that your frontend doesn't currently support widgets.
</p>




```python
dfTravel = dfTravel[dfTravel['Speed_kmh_mean'] <= 12.5]
dfTravel = dfTravel[dfTravel['Speed_kmh_mean'] >= 12.4]
             
def calculate_color(val, maxi, mini):
    """
    Convert the GINI coefficient to a color
    """
            
    # make gini a number between 0 and 1
    normalized_gini = (maxi - val) / (maxi - mini)

    # invert gini so that high inequality gives dark color
    inverse_gini = 1.0 - normalized_gini

    # transform the gini coefficient to a matplotlib color
    mpl_color = binary(inverse_gini)

    # transform from a matplotlib color to a valid CSS color
    gmaps_color = to_hex(mpl_color, keep_alpha=False)

    return gmaps_color

def display_map(arg):
    a = dfTravel[arg]
    a = [calculate_color(x, dfTravel[arg].max(), dfTravel[arg].min()) for x in a]

    fig = gmaps.figure()

    layer = gmaps.symbol_layer(
            dfTravel[["LatitudeMean", "LongitudeMean"]],
        fill_color=a,
        stroke_color=a,
    scale=5)

    fig.add_layer(layer)
    return fig

display_map("Speed_kmh_mean")
```

    /Users/tanguy/anaconda/lib/python2.7/site-packages/ipykernel_launcher.py:10: RuntimeWarning: invalid value encountered in double_scalars
      # Remove the CWD from sys.path while we load stuff.



<p>Failed to display Jupyter Widget of type <code>Figure</code>.</p>
<p>
  If you're reading this message in Jupyter Notebook or JupyterLab, it may mean
  that the widgets JavaScript is still loading. If this message persists, it
  likely means that the widgets JavaScript library is either not installed or
  not enabled. See the <a href="https://ipywidgets.readthedocs.io/en/stable/user_install.html">Jupyter
  Widgets Documentation</a> for setup instructions.
</p>
<p>
  If you're reading this message in another notebook frontend (for example, a static
  rendering on GitHub or <a href="https://nbviewer.jupyter.org/">NBViewer</a>),
  it may mean that your frontend doesn't currently support widgets.
</p>



<img src="screenshot.png">

Les raisons d'une vitesse excessivement lente sur cette portion du boulevard Rosemont peuvent êtres multiples.

Etant donné que les données collectées s'étalent sur un an, il est possible qu'un événement temporaire, comme une construction sur la route, ait influencé les résultats.

Géographiquement, la rue est proche de plusieurs points d'interet : 
- la Bibliothèque Marc-Favreau
- la sortie du métro Rosemont
- Plusieurs espaces de travail

Il faut également savoir que la vitesse est limitée à 40km/h sur cette rue, contrairement à la plupart des rues de Montréal ou la vitesse est limitée a 50. http://journalmetro.com/local/rosemont-la-petite-patrie/actualites/972820/rosemont-reduit-la-vitesse-sur-ses-principales-rues/ 




```python

```
