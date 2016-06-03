---
layout: post
title: Writing an Infinispan Custom Store for JBoss AS7
tags: [java]
---
So there I was, tasked to use Infinispan for fast retrieval of some records from the database. I started researching about the technology and reading the infinispan online documentation.

In the course of learning, I found out about the Infinispan subsystem bundled with JBoss AS7. JBoss AS7 is the applciation server that we are using. As it will help lessen management of different versions of the same system, I decided to just use the version integrated with JBoss AS7. This is when I found out about the lack of documentation for the Infinsipan subsystem.

Augment the lack of documentation with our a slightly unique requirement for retrieving and storing data from the persistent store, this became a daunting task. Since the available cache stores do not support our requirement, we had to create a custom cache store. There is almost no documentation about creating a custom cache store for infinispan and this is a bit disconcerting. To bridge some of the gap, I want to publish the steps that I did to create a custom store.

## Configuration Properties

Almost all new custom cache stores need additional properties and the custom cache store needs to be informed of these properties. A configuration class that will store these additional properties needs to be created.

In Infinispan, the configuration class should extend from `org.infinispan.configuration.cache.AbstractLockSupportStoreConfiguration` and it should implement the `org.infinispan.configuration.cache.LegacyLoaderAdapter<T>` interface. `org.infinispan.configuration.cache.AbstractLockSupportStoreConfiguration` already contains the general infinispan store configurations and only the new properties need to be defined.

If a new property called `batchSize` is needed, this should be declared in the Configuration class. The value of this property can be specified in the constructor. The assignment of this property's value is in constructor. The read accessor for this proeprty does not follow the typical Java getter naming convention, instead it uses the same name as the property's name.

```java
public class PropertyCacheStoreConfiguration
        extends AbstractLockSupportStoreConfiguration
        implements LegacyLoaderAdapter<PropertyCacheStoreConfig> {

  final private long batchSize;

  protected PropertyCacheStoreConfiguration(long batchSize,
          long lockAcquistionTimeout, int lockConcurrencyLevel,
          boolean purgeOnStartup, boolean purgeSynchronously,
          int purgerThreads, boolean fetchPersistentState,
          boolean ignoreModifications, TypedProperties properties,
          AsyncStoreConfiguration async,
          SingletonStoreConfiguration singletonStore) {
    super(lockAcquistionTimeout, lockConcurrencyLevel, purgeOnStartup,
            purgeSynchronously, purgerThreads, fetchPersistentState,
            ignoreModifications, properties, async, singletonStore);

    this.batchSize = batchSize;
  }

  public long batchSize() {
    return batchSize;
  }
}
```


To help build the configuration class, a builder is usually associated with it. The builder is specified in the `org.infinispan.configuration.BuiltBy` annotation of the class. This is what the class declaration should look like after specifying the `BuiltBy` annotation.

```java
@BuiltBy(PropertyCacheStoreConfigurationBuilder.class)
public class PropertyCacheStoreConfiguration
        extends AbstractLockSupportStoreConfiguration
        implements LegacyLoaderAdapter<PropertyCacheStoreConfig> {
  ...
}
```

To create the configuration builder, it should extend the abstract class `org.infinispan.configuration.cache.AbstractLockSupportStoreConfigurationBuilder<T, S>`. The `T` generic type should assigned with the configuration object type and the `S` generic type should be assigned with the configuration builder type.

```java
public class PropertyCacheStoreConfigurationBuilder
        extends AbstractLockSupportStoreConfigurationBuilder<
          PropertyCacheStoreConfiguration,
          PropertyCacheStoreConfigurationBuilder> {

}
```

The `create()` and `read(T)` methods should be implemented. The `create()` method should instantiate the bean where the configuration parameters are kept. The `read(T)` method should populate the configuration object with the correct values. `read(T)` will be called with the configuration properties are read from the XML file. In this method, it is important to note that the configuration object is passed as parameter and properties should be set using this object.


```java
public class PropertyCacheStoreConfigurationBuilder
        extends AbstractLockSupportStoreConfigurationBuilder<
          PropertyCacheStoreConfiguration,
          PropertyCacheStoreConfigurationBuilder> {

  private long batchSize = PropertyCacheStoreConfig.DEFAULT_BATCH_SIZE;

  public PropertyCacheStoreConfigurationBuilder(
      LoadersConfigurationBuilder builder) {
    super(builder);
  }

  public PropertyCacheStoreConfigurationBuilder batchSize(
      long batchSize) {
    this.batchSize = batchSize;
    return self();
  }

  public PropertyCacheStoreConfigurationBuilder self() {
    return this;
  }

  public PropertyCacheStoreConfiguration create() {
    return new PropertyCacheStoreConfiguration(batchSize,
            lockAcquistionTimeout, lockConcurrencyLevel, purgeOnStartup,
            purgeSynchronously, purgerThreads, fetchPersistentState,
            ignoreModifications, TypedProperties.toTypedProperties(properties),
            async.create(), singletonStore.create());
  }

  public Builder<?> read(PropertyCacheStoreConfiguration template) {
    batchSize = template.batchSize();

    // LockSupportStore-specific configuration
    lockAcquistionTimeout = template.lockAcquistionTimeout();
    lockConcurrencyLevel = template.lockConcurrencyLevel();

    // AbstractStore-specific configuration
    fetchPersistentState = template.fetchPersistentState();
    ignoreModifications = template.ignoreModifications();
    properties = template.properties();
    purgeOnStartup = template.purgeOnStartup();
    purgeSynchronously = template.purgeSynchronously();
    this.async.read(template.async());
    this.singletonStore.read(template.singletonStore());

    return self();
  }
}
```

## Parsers

To properly read configuration parameters from the XML file, parsers need to be created. 2 parsers will be created as the configuration XML file for JBoss AS is different from the configuration XML of standalone Infinsipan. A parser should implement the `org.infinispan.configuration.parsing.ConfigurationParser<ConfigurationBuilderHolder>` interface whether its for the AS7 subsystem or the stadalone Infinsipan.

The parser for each version of Infinispan should be appropriately named so as to avoid confusion. For the standalone version, the value "52" is appended to the class name (it is important to note that JBoss AS7 uses Infinispan version 5.2). The version of Infinispan that the parser class supports can be specified in the `getSupportedNamespaces()` of the class.

```java
public class PropertyCacheStoreConfigurationParser52 implements
        ConfigurationParser<ConfigurationBuilderHolder> {
  private static final Namespace NAMESPACES[] = {
      new Namespace(Namespace.INFINISPAN_NS_BASE_URI, "property",
              Element.MIDDLEWARE_PROPERTY_STORE.getLocalName(), 5, 2),
      new Namespace("", Element.MIDDLEWARE_PROPERTY_STORE.getLocalName(), 0, 0)
    };

  public PropertyCacheStoreConfigurationParser52() {
  }

  public Namespace[] getSupportedNamespaces() {
    return NAMESPACES;
  }

  // other methods after this comment
  ...
}
```

The new elements and attributes supported by the new parser should be declared as enumerations. They are named Element.java and Attribute.java, respectively.

The naming convention for elements and attirbutes of the standalone Infinispan 5.2 is camel case.

```java
public enum Element {
  // must be first
  UNKNOWN(null),

  MIDDLEWARE_PROPERTY_STORE("middlewarePropertyStore")

  ;

  private final String name;

  Element(final String name) {
    this.name = name;
  }

  /**
   * Get the local name of this element.
   *
   * @return the local name
   */
  public String getLocalName() {
    return name;
  }

  private static final Map<String, Element> MAP;

  static {
    final Map<String, Element> map = new HashMap<String, Element>(8);
    for (Element element : values()) {
      final String name = element.getLocalName();
      if (name != null) {
        map.put(name, element);
      }
    }
    MAP = map;
  }

  public static Element forName(final String localName) {
    final Element element = MAP.get(localName);
    return element == null ? UNKNOWN : element;
  }
}
```

```java
public enum Attribute {
  // must be first
  UNKNOWN(null),

  BATCH_SIZE("batchSize");

  private final String name;

  private Attribute(final String name) {
    this.name = name;
  }

  /**
   * Get the local name of this element.
   *
   * @return the local name
   */
  public String getLocalName() {
    return name;
  }

  private static final Map<String, Attribute> attributes;

  static {
    final Map<String, Attribute> map = new HashMap<String, Attribute>(64);
    for (Attribute attribute : values()) {
      final String name = attribute.getLocalName();
      if (name != null) {
        map.put(name, attribute);
      }
    }
    attributes = map;
  }

  public static Attribute forName(final String localName) {
    final Attribute attribute = attributes.get(localName);
    return attribute == null ? UNKNOWN : attribute;
  }
}
```

The parser needs to override the `readElement(XMLExtendedStreamReader reader, ConfigurationBuilderHolder holder)` method. A switch statement can be used to read the new element and to parse its attributes. A default block should be defined to handle unknown elements.

```java
public void readElement(XMLExtendedStreamReader reader,
          ConfigurationBuilderHolder holder)
        throws XMLStreamException {
  ConfigurationBuilder builder = holder.getCurrentConfigurationBuilder();

  Element element = Element.forName(reader.getLocalName());
  switch (element) {
    case MIDDLEWARE_PROPERTY_STORE:
      parsePropertyCacheStoreXml(reader, builder.loaders());
      break;
    default: {
      throw ParseUtils.unexpectedElement(reader);
    }
  }

}
```

The `parsePropertyCacheStoreXml()` method is private and it handles reading the attributes. In this method, the switch statement should handle new attributes. Furthermore, the default block should contain the call to the static method `Parser52.parseCommonStoreAttributes` to handle parsing of the common attributes.

```java

private void parsePropertyCacheStoreXml(XMLExtendedStreamReader reader,
        LoadersConfigurationBuilder loadersBuilder)
          throws XMLStreamException {
  PropertyCacheStoreConfigurationBuilder builder =
          new PropertyCacheStoreConfigurationBuilder(loadersBuilder);

  for (int i = 0; i < reader.getAttributeCount(); i++) {
    ParseUtils.requireNoNamespaceAttribute(reader, i);
    String value = replaceProperties(reader.getAttributeValue(i));
    Attribute attribute = Attribute.forName(reader.getAttributeLocalName(i));
    switch (attribute) {
      case BATCH_SIZE: {
        builder.batchSize(Long.valueOf(value));
        break;
      }
      default: {
        Parser52.parseCommonStoreAttributes(reader, i, builder);
        break;
      }
    }
  }

  if (reader.hasNext() && (reader.nextTag() !=
      XMLStreamConstants.END_ELEMENT)) {
    ParseUtils.unexpectedElement(reader);
 }

}
```

Another set of parsers and element and attribute enumerations should be defined for JBoss AS7 as configurations for the application server are defined in either the standalone.xml or domain.xml file. The parsers and enumerations should be almost the same as with the 5.2 version but the naming convention used will be different. The naming convention should follow JBoss AS7's dash-separated names.

```java
 public enum Element {
   // must be first
   UNKNOWN(null),
   MIDDLEWARE_PROPERTY_STORE("middleware-property-store");
   ...
 }
```


```java

public enum Attribute {
  // must be first
  UNKNOWN(null),

  BATCH_SIZE("batch-size");

  private final String name;

  private Attribute(final String name) {
    this.name = name;
  }

  // other methods same as those for version 5.2
}
```

Just as in the 5.2 version, namespaces for AS7 should be declared.

```java
  private static final Namespace NAMESPACES[] = { new Namespace(
          ParserAS7.URN_JBOSS_DOMAIN_INFINISPAN,
          Element.MIDDLEWARE_PROPERTY_STORE.getLocalName(),
          1, 4) };

  public Namespace[] getSupportedNamespaces() {
    return NAMESPACES;
  }
```

The `readElement()` should be implemented and common attributes should be given to `ParserAS7.parseStoreAttribute` for parsing.

```java
public void readElement(XMLExtendedStreamReader reader, ConfigurationBuilderHolder holder)
        throws XMLStreamException {

  Element element = Element.forName(reader.getLocalName());
  switch (element) {
    case MIDDLEWARE_PROPERTY_STORE:
      parsePropertyCacheStoreXml(reader, holder);
      break;
    default: {
      throw ParseUtils.unexpectedElement(reader);
    }
  }

}

private void parsePropertyCacheStoreXml(XMLExtendedStreamReader reader,
        ConfigurationBuilderHolder holder) throws XMLStreamException {
  LoadersConfigurationBuilder loaders = holder.getCurrentConfigurationBuilder().loaders();
  PropertyCacheStoreConfigurationBuilder builder = new PropertyCacheStoreConfigurationBuilder(
          loaders);

  for (int i = 0; i < reader.getAttributeCount(); i++) {
    ParseUtils.requireNoNamespaceAttribute(reader, i);
    String value = replaceProperties(reader.getAttributeValue(i));
    Attribute attribute = Attribute.forName(reader.getAttributeLocalName(i));
    switch (attribute) {
      case BATCH_SIZE: {
        builder.batchSize(Long.valueOf(value));
        break;
      }
      default: {
        ParserAS7.parseStoreAttribute(reader, i, builder);
        break;
      }
    }
  }

  while (reader.hasNext() && (reader.nextTag() != XMLStreamConstants.END_ELEMENT)) {
    ParserAS7.parseStoreElement(reader, builder);
  }

  loaders.addStore(builder);

}
```

## Custom Store

The custom store is responsible for persisting data. It should extend from the abstract `org.infinispan.loaders.LockSupportCacheStore` class with the type of the cache key specified in the generic declaration. Also, the class needs to be annotated with `org.infinispan.loaders.CacheLoaderMetadata` and the type of the builder for the configuration class should be passed as parameter. This is for backward-compatibility.

```java

@CacheLoaderMetadata(configurationClass = PropertyCacheStoreConfig.class)
public class PropertyCacheStore extends LockSupportCacheStore<String> {
}
```

Several methods should be overridden to properly implement the logic for the persistent data store. They are as follows

- `init(CacheLoaderConfig config, Cache<?, ?> cache, StreamingMarshaller m)` - called when the cache loader is initialized.
- `start()` - called when cache is started. Resources that will be used should be instantiated.
- `stop()` - called when cache is stopped. Resources used can be closed.
- `clearLockSafe()` - clears the data from the persistent store
- `Set<InternalCacheEntry> loadAllLockSafe()` - retrieves data from the persistent store. returned data will be loaded to the cache.
- `Set<InternalCacheEntry> loadLockSafe(final int maxEntries)` - same as `loadLockSafe()` but with a defined maximum number of entries.
- `Set<Object> loadAllKeysLockSafe(final Set<Object> keysToExclude)` - retrieves all the keys from the persistent store (excluding those keys specified in the `keysToExclude` parameter) and returns them
- `toStreamLockSafe(final ObjectOutput oos)` - retrieves data from the persistent store and puts them in the `ObjectOutputStream`
- `fromStreamLockSafe(final ObjectInput ois)` - retrieves data from the input stream and persists them in the persistent storage
- `removeLockSafe(final Object key, String lockingKey)` - removes the record with the corresponding key from the data store
- `storeLockSafe(final InternalCacheEntry entry, String lockingKey)` - adds or updates the record in the data store with the value in the Cache
- `loadLockSafe(final Object key, final String lockingKey)` - loads the cache with the record that is associated with the passed in `key` by returning the cache entry of the object from the persistent data store.


# JBoss AS configuration

Additional configurations need to be done for enabling the custom cache store in JBoss AS7.

The first step is to install the custom cache store as a module by placing the jar file in the appropriate subdirectory within the /modules/ directory and declaring the module.xml. The correct dependencies should then be declared.

```xml
<dependencies>
  <module name="javax.api"/>
  <module name="org.jboss.logging"/>
  <module name="org.jboss.as.clustering.infinispan" />

  <module name="org.infinispan" export="true" services="import"/>
  <module name="org.infinispan.query" export="true" services="import"/>

  <!-- Add in other dependencies that the custom store needs -->
</dependencies>

```

The new custom store should be seen by both the AS clustering module and the infinispan module and so the newly added custom store module should be added as their dependencies.

- Add new dependency to modules/system/layers/base/org/jboss/as/clustering/infinispan/main/module.xml.
- Add new dependency to modules/system/layers/base/org/infinispan/main/module.xml

```xml

<module name="com.hp.middleware.prima.middleware-cache-property" optional="true"/>

```

In standalone.xml or domain.xml, cache container and the custom cache store should be declared. For the custom store, the element `store` should be used and the full class path should be declared in the `class` attribute.

```xml
<cache-container name="middleware-property" default-cache="properties-by-key" jndi-name="java:jboss/infinispan/middleware-property" start="EAGER" module="com.hp.middleware.prima.middleware-cache-property">
    <local-cache name="properties-by-key">
        <store class="com.hp.middleware.prima.cache.property.loaders.PropertyCacheStore" shared="true" preload="true" passivation="false" purge="false"/>
    </local-cache>
</cache-container>
```

The following are the descriptions for the cache-container attributes:

- `jndi-name` - so cache manager of this container can be programmatically accessed via this JNDI name
- `module` - class loader is the same as the module.
- `start` - set to "EAGER" so that container will start on start up of application Server

The following are the descriptions for the cache attributes and child elements:

- `name` - name of the cache. Used when `getCache(String)` of cache manager is called in the program.
- `store` - Used for custom cache store declaration
  - `class` - fully-qualified class name of the custom store
  - `shared` - set to true if there's one instance of the persistent data store in all the clustered environment
  - `preload` - Load data to cache on start of cache
  - `passivation` - set to true so that persistent data store is updated immediately on call of `put()` method of the cache interface
  - `purge` - set to false so as not to purge all data on start of cache

# Indexing

Retrieval of records using a property other than the key can be done by turning indexing on. Hibernate search and lucene can then be used for searching for records using the index.

To add this functionality, the infinispan query should be installed as module. The following steps should be followed to install infinispan query:

- Add the directory `..\modules\system\layers\base\org\infinispan\query\main`
- Create a module.xml and specify the following:

```xml
<module xmlns="urn:jboss:module:1.0" name="org.infinispan.query">

	<resources>

		<resource-root path="avro-1.6.3.jar"/>
		<resource-root path="jackson-core-asl-1.8.8.jar"/>
		<resource-root path="jackson-mapper-asl-1.8.8.jar"/>
		<resource-root path="paranamer-2.3.jar"/>
		<resource-root path="snappy-java-1.0.4.1.jar"/>
		<resource-root path="slf4j-api-1.7.5.jar"/>

		<resource-root path="infinispan-query-5.2.7.Final.jar"/>
		<resource-root path="infinispan-lucene-directory-5.2.7.Final.jar"/>

		<resource-root path="hibernate-search-infinispan-4.2.0.Final.jar"/>
		<resource-root path="hibernate-search-engine-4.2.0.Final.jar"/>
		<resource-root path="hibernate-search-4.2.0.Final.jar"/>
		<resource-root path="hibernate-search-orm-4.2.0.Final.jar"/>
		<resource-root path="hibernate-search-analyzers-4.2.0.Final.jar"/>

		<resource-root path="lucene-core-3.6.2.jar"/>
		<resource-root path="lucene-facet-3.6.2.jar"/>
		<resource-root path="lucene-analyzers-3.6.2.jar"/>
		<resource-root path="lucene-smartcn-3.6.2.jar"/>
		<resource-root path="lucene-stempel-3.6.2.jar"/>
		<resource-root path="lucene-highlighter-3.6.2.jar"/>
		<resource-root path="lucene-kuromoji-3.6.2.jar"/>
		<resource-root path="lucene-memory-3.6.2.jar"/>
		<resource-root path="lucene-misc-3.6.2.jar"/>
		<resource-root path="lucene-phonetic-3.6.2.jar"/>
		<resource-root path="lucene-spatial-3.6.2.jar"/>
		<resource-root path="lucene-spellchecker-3.6.2.jar"/>
		<resource-root path="lucene-grouping-3.6.2.jar"/>

		<resource-root path="solr-analysis-extras-3.6.2.jar"/>
		<resource-root path="solr-core-3.6.2.jar"/>
		<resource-root path="solr-solrj-3.6.2.jar"/>

		<resource-root path="guava-r05.jar"/>

	</resources>

	<dependencies>
		<module name="javax.api"/>

		<module name="javax.transaction.api"/>

		<module name="org.hibernate.commons-annotations" services="import"/>
		<module name="org.jgroups"/>
		<module name="org.hibernate" services="import"/>

		<module name="org.infinispan" services="import" export="true"/>

		<module name="org.apache.commons.codec"/>
		<module name="org.apache.commons.io"/>
		<module name="org.apache.commons.lang"/>

		<module name="org.slf4j" export="true"/>

		<module name="org.jboss.logging"/>

	</dependencies>
</module>
```

- Download the correct jar files specified in the resources tag and place them in the same directory.

To enable indexing on a particular field, the object being cached should be annotated with `org.hibernate.search.annotations.Indexed` and the field that should be indexed should be annotated with `org.hibernate.search.annotations.Field`.

```java
@Indexed
public class PropertyTable implements Serializable {

  private static final long serialVersionUID = 1L;

  @Id
  @DocumentId
  private String propertyKey;

  @Field
  private String propertyGroup;
}
```

In the standalone.xml, the following should be defined inside the `cache` element.

```xml
<indexing index="LOCAL">
    <property name="hibernate.search.default.directory_provider">
        ram
    </property>
</indexing>
```
