
# Design Patterns in Java

## 1. Behavioral Design Patterns

### Use Case 1: Traffic Signal System - Observer Pattern

#### Code:

```java
import java.util.ArrayList;
import java.util.List;

// Subject
class TrafficSignal {
    private List<TrafficLight> observers = new ArrayList<>();
    private String state;

    public void addObserver(TrafficLight observer) {
        observers.add(observer);
    }

    public void removeObserver(TrafficLight observer) {
        observers.remove(observer);
    }

    public void setState(String state) {
        this.state = state;
        notifyObservers();
    }

    private void notifyObservers() {
        for (TrafficLight observer : observers) {
            observer.update(state);
        }
    }
}

// Observer
class TrafficLight {
    private String name;

    public TrafficLight(String name) {
        this.name = name;
    }

    public void update(String state) {
        System.out.println(name + " traffic light turned " + state);
    }
}

// Main class to demonstrate the Observer pattern
public class TrafficSignalSystem {
    public static void main(String[] args) {
        TrafficSignal signal = new TrafficSignal();

        TrafficLight light1 = new TrafficLight("North");
        TrafficLight light2 = new TrafficLight("East");

        signal.addObserver(light1);
        signal.addObserver(light2);

        signal.setState("Green");
        signal.setState("Red");
    }
}
```

#### Output:

```
North traffic light turned Green
East traffic light turned Green
North traffic light turned Red
East traffic light turned Red
```

---

### Use Case 2: Text Editor Undo/Redo - Memento Pattern

#### Code:

```java
class Editor {
    private String content = "";

    public void type(String words) {
        content += words;
    }

    public String getContent() {
        return content;
    }

    public EditorMemento save() {
        return new EditorMemento(content);
    }

    public void restore(EditorMemento memento) {
        content = memento.getContent();
    }
}

class EditorMemento {
    private final String content;

    public EditorMemento(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }
}

public class TextEditor {
    public static void main(String[] args) {
        Editor editor = new Editor();

        editor.type("Hello, ");
        editor.type("World!");
        System.out.println("Current content: " + editor.getContent());

        EditorMemento savedState = editor.save();

        editor.type(" More text.");
        System.out.println("Modified content: " + editor.getContent());

        editor.restore(savedState);
        System.out.println("Restored content: " + editor.getContent());
    }
}
```

#### Output:

```
Current content: Hello, World!
Modified content: Hello, World! More text.
Restored content: Hello, World!
```

---

## 2. Creational Design Patterns

### Use Case 3: Database Connection Pool - Singleton Pattern

#### Code:

```java
class DatabaseConnectionPool {
    private static DatabaseConnectionPool instance;
    private int availableConnections = 2;

    private DatabaseConnectionPool() {
    }

    public static synchronized DatabaseConnectionPool getInstance() {
        if (instance == null) {
            instance = new DatabaseConnectionPool();
        }
        return instance;
    }

    public String getConnection() {
        if (availableConnections > 0) {
            availableConnections--;
            return "Connection acquired.";
        } else {
            return "No available connections.";
        }
    }

    public void releaseConnection() {
        availableConnections++;
    }
}

public class ConnectionPoolDemo {
    public static void main(String[] args) {
        DatabaseConnectionPool pool1 = DatabaseConnectionPool.getInstance();
        DatabaseConnectionPool pool2 = DatabaseConnectionPool.getInstance();

        System.out.println(pool1.getConnection());  // Connection acquired.
        System.out.println(pool2.getConnection());  // Connection acquired.
        System.out.println(pool1.getConnection());  // No available connections.
        
        pool1.releaseConnection();
        System.out.println(pool2.getConnection());  // Connection acquired.

        System.out.println(pool1 == pool2);  // true
    }
}
```

#### Output:

```
Connection acquired.
Connection acquired.
No available connections.
Connection acquired.
true
```

---

### Use Case 4: Computer Configurator - Abstract Factory Pattern

#### Code:

```java
interface CPU {
    String getDescription();
}

interface GPU {
    String getDescription();
}

class GamingCPU implements CPU {
    public String getDescription() {
        return "High-performance Gaming CPU";
    }
}

class WorkstationCPU implements CPU {
    public String getDescription() {
        return "Multi-core Workstation CPU";
    }
}

class GamingGPU implements GPU {
    public String getDescription() {
        return "High-end Gaming GPU";
    }
}

class WorkstationGPU implements GPU {
    public String getDescription() {
        return "Professional Workstation GPU";
    }
}

interface ComputerFactory {
    CPU createCPU();
    GPU createGPU();
}

class GamingComputerFactory implements ComputerFactory {
    public CPU createCPU() {
        return new GamingCPU();
    }

    public GPU createGPU() {
        return new GamingGPU();
    }
}

class WorkstationComputerFactory implements ComputerFactory {
    public CPU createCPU() {
        return new WorkstationCPU();
    }

    public GPU createGPU() {
        return new WorkstationGPU();
    }
}

public class ComputerConfigurator {
    public static void main(String[] args) {
        ComputerFactory gamingFactory = new GamingComputerFactory();
        CPU gamingCPU = gamingFactory.createCPU();
        GPU gamingGPU = gamingFactory.createGPU();
        System.out.println(gamingCPU.getDescription());
        System.out.println(gamingGPU.getDescription());

        ComputerFactory workstationFactory = new WorkstationComputerFactory();
        CPU workstationCPU = workstationFactory.createCPU();
        GPU workstationGPU = workstationFactory.createGPU();
        System.out.println(workstationCPU.getDescription());
        System.out.println(workstationGPU.getDescription());
    }
}
```

#### Output:

```
High-performance Gaming CPU
High-end Gaming GPU
Multi-core Workstation CPU
Professional Workstation GPU
```

---

## 3. Structural Design Patterns

### Use Case 5: Smart Home Device Control - Facade Pattern

#### Code:

```java
class Lights {
    public void on() {
        System.out.println("Lights turned on");
    }

    public void off() {
        System.out.println("Lights turned off");
    }
}

class AirConditioning {
    public void start() {
        System.out.println("Air Conditioning started");
    }

    public void stop() {
        System.out.println("Air Conditioning stopped");
    }
}

class SecuritySystem {
    public void arm() {
        System.out.println("Security System armed");
    }

    public void disarm() {
        System.out.println("Security System disarmed");
    }
}

class SmartHomeFacade {
    private Lights lights;
    private AirConditioning ac;
    private SecuritySystem security;

    public SmartHomeFacade() {
        this.lights = new Lights();
        this.ac = new AirConditioning();
        this.security = new SecuritySystem();
    }

    public void startDay() {
        lights.on();
        ac.start();
        security.disarm();
    }

    public void endDay() {
        lights.off();
        ac.stop();
        security.arm();
    }
}

public class SmartHomeController {
    public static void main(String[] args) {
        SmartHomeFacade smartHome = new SmartHomeFacade();
        smartHome.startDay();
        smartHome.endDay();
    }
}
```

#### Output:

```
Lights turned on
Air Conditioning started
Security System disarmed
Lights turned off
Air Conditioning stopped
Security System armed
```

---

### Use Case 6: Social Media Sharing - Proxy Pattern

#### Code:

```java
interface Video {
    void play();
}

class RealVideo implements Video {
    private String filename;

    public RealVideo(String filename) {
        this.filename = filename;
        loadVideo();
    }

    private void loadVideo() {
        System.out.println("Loading video: " + filename);
    }

    public void play() {
        System.out.println("Playing video: " + filename);
    }
}

class VideoProxy implements Video {
    private RealVideo realVideo;
    private String filename;

    public VideoProxy(String filename) {
        this.filename = filename;
    }

    public void play() {
        if (realVideo == null) {
            realVideo = new RealVideo(filename);
        }
        realVideo.play();
    }
}

public class ProxyPatternDemo {
    public static void main(String[] args) {
        Video video = new VideoProxy("cat_video.mp4");
        video.play();  // Loading and playing
        video.play();  // Just playing, no loading this time
    }
}
```

#### Output:

```
Loading video: cat_video.mp4
Playing video: cat_video.mp4
Playing video: cat_video.mp4
```
