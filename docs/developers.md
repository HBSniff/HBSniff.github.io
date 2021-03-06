# Guide for Developers

## Compilation       
Video Demo of Compilation and Execution: https://doi.org/10.6084/m9.figshare.16682029         

1. Clone [the GitHub repository](https://github.com/HBSniff/HBSniff) of HBSniff. 
2. Open it with an IDE, we suggest using IntelliJ IDEA. 
3. Since the dependencies and compilation of the project is managed by Maven 5, a ```maven clean install``` command will automatically download dependencies and generate an executable jar file in the ```target``` folder, e.g., HBSniff-1.6.8-jar-with-dependencies.jar.

## Architecture
![architecture](architecture.png)

HBSniff consists of 3 major modules (sub-packages), i.e., ```model, parser, detector```. The general architecture is depicted in the figure. The light gray colored box represents the scope of HBSniff code, while the dark gray colored boxes are sub-packages of HBSniff. The blue-colored boxes are classes in the sub-packages, and the white-colored boxes are external libraries and resources.        

First, users specify the path of the project to detect as input (the ```-i``` parameter) and the path of the output directory (the ```-o``` parameter). Then, the program constructs higher level abstractions (whose data models are available in the ```model``` sub-package) in the ```parser``` sub-package using ```JavaParser```. Meanwhile, it locates HQL in the context of the method and class containing the method call. Moreover, it finds the method that calls the method containing HQL (```CalledIn``` methods in the figure). Afterwards, the generated ```models``` will be used to populate the context of code smell ```detectors```. Finally, the detection and metric evaluation will be performed, and the results will be converted to Excel reports as well as csv and JSON data.          

## Implement a new smell detector

1. Create a detector class named ```$detectorName$``` implementing ```SmellDetector``` in ```io.github.hzjdev.hbsniff.detector.rules``` package.
2. Implement the ```exec()``` method from the ```SmellDetector``` interface, detection rules could be specified here. To use the parsed ASTs and ```Declarations```, please refer to the [SmellDetector](smellDetector.md) section for more details.       
3. Add a ```SmellType Enumeration``` named ```$detectorName$``` in the ```SmellDetectorFactory``` class, this will allow the static factory to automatically initialize the implemented detector.  
4. Do not forget to implement unit tests for the new detectors.

## Implement a new metric

For a better performance, currently all mapping metrics are implemented in ```io.github.hzjdev.hbsniff.metric.MappingMetrics``` since they share similar logics and could be evaluated in a similar fashion. 

To implement different metrics, we suggest following the architecture of smell detectors, i.e., create a static factory and related ```Enumerations```. The architecture of metric evaluation may be improved in the future.     

See also the [Mapping Metrics](mappingMetricsEvaluator.md) section for more information.

## Evaluate a project without specifying commandline parametres
Please refer to the first 2 ```String``` constants of ``` io.github.hzjdev.hbsniff.utils.Const``` class.

```java
    public final static String DEFAULT_INPUT_PATH = "D:\\tools\\hql\\projects\\BroadleafCommerce";
    public final static String DEFAULT_OUTPUT_PATH = "D:\\tools\\hql\\projects";
```
If ```-i``` and ```-o``` is not specified, an error will be raised, but HBSniff will still try to execute according to these 2 default parametres.