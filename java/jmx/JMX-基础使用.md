# JMX-基础使用

Java JMX (Java Management Extensions) 是 Java 平台提供的一套管理和监控应用程序的标准框架。它允许开发者创建可管理的资源，并通过标准的管理工具进行监控和操作。

## 1.JMX 核心概念

**MBean (Managed Bean)**
MBean 是 JMX 中的核心概念，代表可管理的资源。主要有四种类型：
- Standard MBean：通过接口定义管理操作
- Dynamic MBean：运行时动态定义管理操作
- Open MBean：使用开放数据类型
- Model MBean：最灵活的类型，支持通知和描述符

**MBean Server**
MBean Server 是 JMX 代理的核心组件，负责注册和管理所有的 MBean。它提供了创建、注册、注销 MBean 的功能，并处理对 MBean 的管理操作。

**Object Name**
每个 MBean 在 MBean Server 中都有唯一的 Object Name，格式为 `domain:key1=value1,key2=value2`。

## 2. Standard MBean 示例

首先定义管理接口：

```java
public interface SystemInfoMBean {
    // 属性 - 可读
    long getUptime();
    
    // 属性 - 可读写
    String getSystemName();
    void setSystemName(String name);
    
    // 操作
    String getSystemInfo();
    void gc();
    
    // 只读属性
    int getThreadCount();
}

```

实现类：

```java

import java.lang.management.ManagementFactory;
import java.lang.management.RuntimeMXBean;
import java.lang.management.ThreadMXBean;

public class SystemInfo implements SystemInfoMBean {
    private String systemName = "MyApplication";
    private final RuntimeMXBean runtimeMXBean;
    private final ThreadMXBean threadMXBean;
    
    public SystemInfo() {
        this.runtimeMXBean = ManagementFactory.getRuntimeMXBean();
        this.threadMXBean = ManagementFactory.getThreadMXBean();
    }
    
    @Override
    public long getUptime() {
        return runtimeMXBean.getUptime();
    }
    
    @Override
    public String getSystemName() {
        return systemName;
    }
    
    @Override
    public void setSystemName(String name) {
        this.systemName = name;
    }
    
    @Override
    public String getSystemInfo() {
        return String.format("System: %s, Uptime: %d ms, Threads: %d", 
                           systemName, getUptime(), getThreadCount());
    }
    
    @Override
    public void gc() {
        System.gc();
        System.out.println("Garbage collection requested");
    }
    
    @Override
    public int getThreadCount() {
        return threadMXBean.getThreadCount();
    }
}
```

## 3. JMX 服务器和客户端

创建 JMX 服务器：

```java
import javax.management.*;
import javax.management.remote.*;
import java.lang.management.ManagementFactory;
import java.rmi.registry.LocateRegistry;

public class JMXServer {
    
    public static void main(String[] args) throws Exception {
        // 创建 MBean Server
        MBeanServer server = ManagementFactory.getPlatformMBeanServer();
        
        // 创建并注册 MBean
        SystemInfo systemInfo = new SystemInfo();
        ObjectName objectName = new ObjectName("com.example:type=SystemInfo");
        server.registerMBean(systemInfo, objectName);
        
        // 创建 RMI 注册表
        LocateRegistry.createRegistry(9999);
        
        // 创建 JMX 连接器服务器
        JMXServiceURL url = new JMXServiceURL(
            "service:jmx:rmi:///jndi/rmi://localhost:9999/jmxrmi");
        JMXConnectorServer connectorServer = 
            JMXConnectorServerFactory.newJMXConnectorServer(url, null, server);
        
        // 启动连接器服务器
        connectorServer.start();
        
        System.out.println("JMX Server started at: " + url);
        System.out.println("Press Enter to stop...");
        System.in.read();
        
        connectorServer.stop();
    }
}

```

JMX 客户端：

```java
import javax.management.*;
import javax.management.remote.*;

public class JMXClient {
    
    public static void main(String[] args) throws Exception {
        // 连接到 JMX 服务器
        JMXServiceURL url = new JMXServiceURL(
            "service:jmx:rmi:///jndi/rmi://localhost:9999/jmxrmi");
        JMXConnector connector = JMXConnectorFactory.connect(url);
        MBeanServerConnection mbsc = connector.getMBeanServerConnection();
        
        // 创建 ObjectName
        ObjectName objectName = new ObjectName("com.example:type=SystemInfo");
        
        try {
            // 读取属性
            String systemName = (String) mbsc.getAttribute(objectName, "SystemName");
            Long uptime = (Long) mbsc.getAttribute(objectName, "Uptime");
            Integer threadCount = (Integer) mbsc.getAttribute(objectName, "ThreadCount");
            
            System.out.println("System Name: " + systemName);
            System.out.println("Uptime: " + uptime + " ms");
            System.out.println("Thread Count: " + threadCount);
            
            // 修改属性
            mbsc.setAttribute(objectName, new Attribute("SystemName", "UpdatedSystem"));
            
            // 调用操作
            String systemInfo = (String) mbsc.invoke(objectName, "getSystemInfo", null, null);
            System.out.println("System Info: " + systemInfo);
            
            // 调用无参操作
            mbsc.invoke(objectName, "gc", null, null);
            
        } finally {
            connector.close();
        }
    }
}

```

## 4. Dynamic MBean 示例

Dynamic MBean 提供更大的灵活性：

```java
import javax.management.*;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

public class ConfigurationMBean implements DynamicMBean {
    private final Map<String, Object> configuration = new ConcurrentHashMap<>();
    
    public ConfigurationMBean() {
        configuration.put("maxConnections", 100);
        configuration.put("timeout", 30000L);
        configuration.put("enabled", true);
    }
    
    @Override
    public Object getAttribute(String attribute) throws AttributeNotFoundException {
        if (configuration.containsKey(attribute)) {
            return configuration.get(attribute);
        }
        throw new AttributeNotFoundException("Attribute " + attribute + " not found");
    }
    
    @Override
    public void setAttribute(Attribute attribute) throws InvalidAttributeValueException {
        String name = attribute.getName();
        Object value = attribute.getValue();
        
        // 验证属性值
        if ("maxConnections".equals(name) && !(value instanceof Integer)) {
            throw new InvalidAttributeValueException("maxConnections must be Integer");
        }
        if ("timeout".equals(name) && !(value instanceof Long)) {
            throw new InvalidAttributeValueException("timeout must be Long");
        }
        if ("enabled".equals(name) && !(value instanceof Boolean)) {
            throw new InvalidAttributeValueException("enabled must be Boolean");
        }
        
        configuration.put(name, value);
    }
    
    @Override
    public AttributeList getAttributes(String[] attributes) {
        AttributeList list = new AttributeList();
        for (String attr : attributes) {
            try {
                list.add(new Attribute(attr, getAttribute(attr)));
            } catch (AttributeNotFoundException e) {
                // 忽略不存在的属性
            }
        }
        return list;
    }
    
    @Override
    public AttributeList setAttributes(AttributeList attributes) {
        AttributeList result = new AttributeList();
        for (Attribute attr : attributes.asList()) {
            try {
                setAttribute(attr);
                result.add(attr);
            } catch (Exception e) {
                // 忽略设置失败的属性
            }
        }
        return result;
    }
    
    @Override
    public Object invoke(String actionName, Object[] params, String[] signature) 
            throws MBeanException, ReflectionException {
        
        if ("reset".equals(actionName)) {
            configuration.clear();
            configuration.put("maxConnections", 100);
            configuration.put("timeout", 30000L);
            configuration.put("enabled", true);
            return "Configuration reset";
        } else if ("getConfig".equals(actionName)) {
            return configuration.toString();
        }
        
        throw new ReflectionException(new NoSuchMethodException(actionName));
    }
    
    @Override
    public MBeanInfo getMBeanInfo() {
        // 定义属性
        MBeanAttributeInfo[] attributes = {
            new MBeanAttributeInfo("maxConnections", "java.lang.Integer", 
                                 "Maximum connections", true, true, false),
            new MBeanAttributeInfo("timeout", "java.lang.Long", 
                                 "Timeout in milliseconds", true, true, false),
            new MBeanAttributeInfo("enabled", "java.lang.Boolean", 
                                 "Service enabled", true, true, true)
        };
        
        // 定义操作
        MBeanOperationInfo[] operations = {
            new MBeanOperationInfo("reset", "Reset configuration to defaults", 
                                 null, "java.lang.String", MBeanOperationInfo.ACTION),
            new MBeanOperationInfo("getConfig", "Get current configuration", 
                                 null, "java.lang.String", MBeanOperationInfo.INFO)
        };
        
        return new MBeanInfo(this.getClass().getName(), 
                           "Dynamic Configuration MBean", 
                           attributes, null, operations, null);
    }
}

```

## 5. JMX 通知机制

支持通知的 MBean：

```java

import javax.management.*;
import java.util.concurrent.atomic.AtomicLong;

public class EventNotificationMBean extends NotificationBroadcasterSupport 
        implements EventNotificationMBeanInterface {
    
    private final AtomicLong sequenceNumber = new AtomicLong(0);
    private volatile boolean alertsEnabled = true;
    private int errorCount = 0;
    
    public static final String ERROR_NOTIFICATION = "error.notification";
    public static final String WARNING_NOTIFICATION = "warning.notification";
    public static final String INFO_NOTIFICATION = "info.notification";
    
    @Override
    public void simulateError() {
        errorCount++;
        if (alertsEnabled) {
            Notification notification = new Notification(
                ERROR_NOTIFICATION,
                this,
                sequenceNumber.incrementAndGet(),
                System.currentTimeMillis(),
                "Error occurred: " + errorCount
            );
            notification.setUserData("ErrorCount=" + errorCount);
            sendNotification(notification);
        }
    }
    
    @Override
    public void simulateWarning(String message) {
        if (alertsEnabled) {
            Notification notification = new Notification(
                WARNING_NOTIFICATION,
                this,
                sequenceNumber.incrementAndGet(),
                System.currentTimeMillis(),
                "Warning: " + message
            );
            sendNotification(notification);
        }
    }
    
    @Override
    public void sendInfo(String info) {
        Notification notification = new Notification(
            INFO_NOTIFICATION,
            this,
            sequenceNumber.incrementAndGet(),
            System.currentTimeMillis(),
            "Info: " + info
        );
        sendNotification(notification);
    }
    
    @Override
    public boolean getAlertsEnabled() {
        return alertsEnabled;
    }
    
    @Override
    public void setAlertsEnabled(boolean enabled) {
        this.alertsEnabled = enabled;
    }
    
    @Override
    public int getErrorCount() {
        return errorCount;
    }
    
    @Override
    public void resetErrorCount() {
        this.errorCount = 0;
    }
    
    @Override
    public MBeanNotificationInfo[] getNotificationInfo() {
        return new MBeanNotificationInfo[] {
            new MBeanNotificationInfo(
                new String[] { ERROR_NOTIFICATION },
                Notification.class.getName(),
                "Error notifications"
            ),
            new MBeanNotificationInfo(
                new String[] { WARNING_NOTIFICATION },
                Notification.class.getName(),
                "Warning notifications"
            ),
            new MBeanNotificationInfo(
                new String[] { INFO_NOTIFICATION },
                Notification.class.getName(),
                "Info notifications"
            )
        };
    }
}

interface EventNotificationMBeanInterface {
    void simulateError();
    void simulateWarning(String message);
    void sendInfo(String info);
    boolean getAlertsEnabled();
    void setAlertsEnabled(boolean enabled);
    int getErrorCount();
    void resetErrorCount();
}
```

通知监听器：

```java

import javax.management.*;
import java.lang.management.ManagementFactory;

public class JMXNotificationExample {
    
    public static void main(String[] args) throws Exception {
        MBeanServer server = ManagementFactory.getPlatformMBeanServer();
        
        // 注册通知 MBean
        EventNotificationMBean notificationMBean = new EventNotificationMBean();
        ObjectName objectName = new ObjectName("com.example:type=EventNotification");
        server.registerMBean(notificationMBean, objectName);
        
        // 创建通知监听器
        NotificationListener listener = new NotificationListener() {
            @Override
            public void handleNotification(Notification notification, Object handback) {
                System.out.printf("[%s] %s: %s (SeqNum: %d)%n",
                    new java.util.Date(notification.getTimeStamp()),
                    notification.getType(),
                    notification.getMessage(),
                    notification.getSequenceNumber()
                );
                
                if (notification.getUserData() != null) {
                    System.out.println("  UserData: " + notification.getUserData());
                }
            }
        };
        
        // 添加监听器 - 监听所有通知
        server.addNotificationListener(objectName, listener, null, null);
        
        // 创建过滤器 - 只监听错误通知
        NotificationFilter errorFilter = new NotificationFilter() {
            @Override
            public boolean isNotificationEnabled(Notification notification) {
                return EventNotificationMBean.ERROR_NOTIFICATION.equals(notification.getType());
            }
        };
        
        NotificationListener errorListener = new NotificationListener() {
            @Override
            public void handleNotification(Notification notification, Object handback) {
                System.err.println("ERROR ALERT: " + notification.getMessage());
            }
        };
        
        server.addNotificationListener(objectName, errorListener, errorFilter, "ErrorHandler");
        
        // 模拟事件
        System.out.println("Sending notifications...");
        notificationMBean.sendInfo("Application started");
        Thread.sleep(1000);
        
        notificationMBean.simulateWarning("Low memory");
        Thread.sleep(1000);
        
        notificationMBean.simulateError();
        Thread.sleep(1000);
        
        notificationMBean.simulateError();
        Thread.sleep(1000);
        
        // 禁用警报
        notificationMBean.setAlertsEnabled(false);
        notificationMBean.simulateError(); // 这个不会发送通知
        
        // 重新启用
        notificationMBean.setAlertsEnabled(true);
        notificationMBean.sendInfo("Alerts re-enabled");
        
        System.out.println("Total errors: " + notificationMBean.getErrorCount());
        
        // 清理
        server.removeNotificationListener(objectName, listener);
        server.removeNotificationListener(objectName, errorListener);
        server.unregisterMBean(objectName);
    }
}
```

## 6. 使用内置的 Platform MBeans

Java 提供了许多内置的 Platform MBeans：

```java

import javax.management.MBeanServer;
import javax.management.ObjectName;
import java.lang.management.*;

public class PlatformMBeansExample {
    
    public static void main(String[] args) throws Exception {
        // 获取平台 MBean 服务器
        MBeanServer server = ManagementFactory.getPlatformMBeanServer();
        
        // 1. Runtime MXBean
        RuntimeMXBean runtimeMXBean = ManagementFactory.getRuntimeMXBean();
        System.out.println("=== Runtime Information ===");
        System.out.println("JVM Name: " + runtimeMXBean.getVmName());
        System.out.println("JVM Version: " + runtimeMXBean.getVmVersion());
        System.out.println("Uptime: " + runtimeMXBean.getUptime() + " ms");
        System.out.println("Input Arguments: " + runtimeMXBean.getInputArguments());
        
        // 2. Memory MXBean
        MemoryMXBean memoryMXBean = ManagementFactory.getMemoryMXBean();
        System.out.println("\n=== Memory Information ===");
        MemoryUsage heapUsage = memoryMXBean.getHeapMemoryUsage();
        System.out.println("Heap Memory:");
        System.out.println("  Init: " + formatBytes(heapUsage.getInit()));
        System.out.println("  Used: " + formatBytes(heapUsage.getUsed()));
        System.out.println("  Committed: " + formatBytes(heapUsage.getCommitted()));
        System.out.println("  Max: " + formatBytes(heapUsage.getMax()));
        
        MemoryUsage nonHeapUsage = memoryMXBean.getNonHeapMemoryUsage();
        System.out.println("Non-Heap Memory:");
        System.out.println("  Used: " + formatBytes(nonHeapUsage.getUsed()));
        System.out.println("  Committed: " + formatBytes(nonHeapUsage.getCommitted()));
        
        // 3. Thread MXBean
        ThreadMXBean threadMXBean = ManagementFactory.getThreadMXBean();
        System.out.println("\n=== Thread Information ===");
        System.out.println("Thread Count: " + threadMXBean.getThreadCount());
        System.out.println("Peak Thread Count: " + threadMXBean.getPeakThreadCount());
        System.out.println("Daemon Thread Count: " + threadMXBean.getDaemonThreadCount());
        System.out.println("Total Started Thread Count: " + threadMXBean.getTotalStartedThreadCount());
        
        // 获取死锁信息
        long[] deadlockedThreads = threadMXBean.findDeadlockedThreads();
        if (deadlockedThreads != null) {
            System.out.println("Deadlocked threads found: " + deadlockedThreads.length);
        } else {
            System.out.println("No deadlocked threads");
        }
        
        // 4. Garbage Collector MXBeans
        System.out.println("\n=== Garbage Collector Information ===");
        for (GarbageCollectorMXBean gcMXBean : ManagementFactory.getGarbageCollectorMXBeans()) {
            System.out.println("GC Name: " + gcMXBean.getName());
            System.out.println("  Collection Count: " + gcMXBean.getCollectionCount());
            System.out.println("  Collection Time: " + gcMXBean.getCollectionTime() + " ms");
            System.out.println("  Memory Pool Names: " + gcMXBean.getMemoryPoolNames().length);
        }
        
        // 5. Memory Pool MXBeans
        System.out.println("\n=== Memory Pool Information ===");
        for (MemoryPoolMXBean poolMXBean : ManagementFactory.getMemoryPoolMXBeans()) {
            System.out.println("Pool Name: " + poolMXBean.getName());
            System.out.println("  Type: " + poolMXBean.getType());
            MemoryUsage usage = poolMXBean.getUsage();
            if (usage != null) {
                System.out.println("  Used: " + formatBytes(usage.getUsed()));
                System.out.println("  Max: " + formatBytes(usage.getMax()));
            }
        }
        
        // 6. Operating System MXBean
        OperatingSystemMXBean osMXBean = ManagementFactory.getOperatingSystemMXBean();
        System.out.println("\n=== Operating System Information ===");
        System.out.println("OS Name: " + osMXBean.getName());
        System.out.println("OS Version: " + osMXBean.getVersion());
        System.out.println("OS Architecture: " + osMXBean.getArch());
        System.out.println("Available Processors: " + osMXBean.getAvailableProcessors());
        System.out.println("System Load Average: " + osMXBean.getSystemLoadAverage());
        
        // 7. Class Loading MXBean
        ClassLoadingMXBean classLoadingMXBean = ManagementFactory.getClassLoadingMXBean();
        System.out.println("\n=== Class Loading Information ===");
        System.out.println("Loaded Class Count: " + classLoadingMXBean.getLoadedClassCount());
        System.out.println("Total Loaded Class Count: " + classLoadingMXBean.getTotalLoadedClassCount());
        System.out.println("Unloaded Class Count: " + classLoadingMXBean.getUnloadedClassCount());
        
        // 8. Compilation MXBean
        CompilationMXBean compilationMXBean = ManagementFactory.getCompilationMXBean();
        if (compilationMXBean != null) {
            System.out.println("\n=== Compilation Information ===");
            System.out.println("Compiler Name: " + compilationMXBean.getName());
            if (compilationMXBean.isCompilationTimeMonitoringSupported()) {
                System.out.println("Total Compilation Time: " + compilationMXBean.getTotalCompilationTime() + " ms");
            }
        }
        
        // 演示通过 ObjectName 访问
        System.out.println("\n=== Access via ObjectName ===");
        ObjectName runtimeObjectName = new ObjectName(ManagementFactory.RUNTIME_MXBEAN_NAME);
        String vmName = (String) server.getAttribute(runtimeObjectName, "VmName");
        System.out.println("VM Name (via ObjectName): " + vmName);
    }
    
    private static String formatBytes(long bytes) {
        if (bytes < 0) return "N/A";
        if (bytes < 1024) return bytes + " B";
        if (bytes < 1024 * 1024) return String.format("%.2f KB", bytes / 1024.0);
        if (bytes < 1024 * 1024 * 1024) return String.format("%.2f MB", bytes / (1024.0 * 1024));
        return String.format("%.2f GB", bytes / (1024.0 * 1024 * 1024));
    }
}
```




