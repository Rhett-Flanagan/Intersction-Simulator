# Intersction-Simulator
A simulator for testing neuro-evolved autonomous vehicle controllers in intersections, originally created created by Matthew P. Cherry.

## Compilation
The simulator is compiled through [Maven](https://maven.apache.org/).

It can be compiled using:
```mvn clean package```

The compiled file can then be found in the *target* directory.

## Running and Using the Simulator

### Running the Simulator and Basic Functionality
To run the simulator call:
```java -jar <path_to_simulator_jar> <path_to_parameters.json>```

The parameters.json can be edited to simulate different configurations, tracks, contollers etc.

The simulator running can be paused by pressing the space bar, and clicking on a car will display the range of any active sensors.

### Using the Simulator for Experiments.
The 'Main' and 'Trial classes in the 'trial' package provide the framework for trial creation and execution. It is reccomended that a seperate Maven module is setup with your own main method for running your experiment, with the 'pom.xml' file pointing to that to allow for seperate compilations of the simulator and experimentes.

#### Trials
A trial is just a single run of the simulator for a given set of parameters (see `sample_parameters.json`).
Currently you can either run a trial with the renderer to see the GUI or you can run it headless. The headless run should be used for training.

There is a sample trial file in resources. It differs from sample_parameters.json with the neural network field. This field is where the candidate neural netowrk to be evaluated is inserted.

The trial expects a serialised Encog neural network (BasicNetwork, NEAT or HyperNEAT) that should have the following approximate structure:
```
import org.apache.commons.lang3.SerializationUtils;
...
BasicNetwork nn = new BasicNetwork();
SerializationUtils.serialize(nn);
```

#### Tracks
Tracks are encoded in JSON, and are composed of bezier curves with a degree of up to 4.

Additionally the file must contain the following:

* name - Name of the track
* created _(optional)_ - Date of creation in format dd-mm-yyyy
* description _(optional)_ - Description of the track
* curves - Curve data

The _curves_ field must have a list of dictionaries, each with fields:

* degree - The degree of the curve, minimum 2 and maximum 4
* precision - The number of points to have along the curve, minimum 2, defaults to 2
* x\__n_ - The x position of the nth point of the bezier curve
* y\__n_ - The y position of the nth point of the bezier curve

Example:

```json
{
  "name": "Sample",
  "created": "30-12-2017",
  "description": "Sample track",
  "curves":
  [
    {"degree": 2, "x_0": 10, "y_0": 10, "x_1": 50, "y_1": 50},
    {"degree": 3, "precision": 10, "x_0": 10, "y_0": 10, "x_1": 50, "y_1": 50, "x_2": 100, "y_2": 200}
  ]
}
```

If a curve has a start point that is the same as the end point of another curve, the two curves will be linked.
If there is a set of curves that forms a circular pattern with no non-circular curve to feed into it, the entire 
circular pattern will be removed.

Pedestrian tracks are just curves that have a pedestrian value set to true.
