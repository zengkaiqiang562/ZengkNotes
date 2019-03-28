Android Gradle Plugin: 3.0.0
Gradle: 4.1

org.gradle.api.Project  extends Comparable<Project>, ExtensionAware, PluginAware
	void subprojects(@DelegatesTo(value = Project.class, strategy = Closure.DELEGATE_FIRST) @ClosureParams(value = SimpleType.class, options = {"org.gradle.api.Project"}) Closure configureClosure);

	void subprojects(Action<? super Project> action);

	void allprojects(@DelegatesTo(value = Project.class, strategy = Closure.DELEGATE_FIRST) @ClosureParams(value = SimpleType.class, options = {"org.gradle.api.Project"}) Closure configureClosure);

	void allprojects(Action<? super Project> action);

	void repositories(@DelegatesTo(value = RepositoryHandler.class, strategy = Closure.DELEGATE_FIRST) @ClosureParams(value = SimpleType.class, options = {"org.gradle.api.artifacts.dsl.RepositoryHandler"}) Closure configureClosure);

	Task task(Map<String, ?> args, String name, @DelegatesTo(value = Task.class, strategy = Closure.DELEGATE_FIRST) @ClosureParams(ReturnType.class) Closure configureClosure);

	Task task(String name, @DelegatesTo(value = Task.class, strategy = Closure.DELEGATE_FIRST) @ClosureParams(ReturnType.class) Closure configureClosure);

	Task task(String name) throws InvalidUserDataException;

	Task task(Map<String, ?> args, String name) throws InvalidUserDataException;

	TaskContainer getTasks();

	boolean hasProperty(String propertyName); 检查是否存在属性propertyName
	Object property(String propertyName) throws MissingPropertyException;

	void buildscript(@DelegatesTo(value = ScriptHandler.class, strategy = Closure.DELEGATE_FIRST) @ClosureParams(value = SimpleType.class, options = {"org.gradle.api.initialization.dsl.ScriptHandler"}) Closure configureClosure);	

	void dependencies(@DelegatesTo(value = DependencyHandler.class, strategy = Closure.DELEGATE_FIRST) @ClosureParams(value = SimpleType.class, options = {"org.gradle.api.artifacts.dsl.DependencyHandler"}) Closure configureClosure);

	void artifacts(@DelegatesTo(value = ArtifactHandler.class, strategy = Closure.DELEGATE_FIRST) @ClosureParams(value = SimpleType.class, options = {"org.gradle.api.artifacts.dsl.ArtifactHandler"}) Closure configureClosure);

	void artifacts(Action<? super ArtifactHandler> configureAction);

	File file(Object path);
	ConfigurableFileCollection files(Object... paths);
	ConfigurableFileTree fileTree(Map<String, ?> args);

	Project project(String path) throws UnknownProjectException;

	File getProjectDir();

	ExecResult exec(@DelegatesTo(value = ExecSpec.class, strategy = Closure.DELEGATE_FIRST) @ClosureParams(value = SimpleType.class, options = {"org.gradle.process.ExecSpec"}) Closure closure);

	ExecResult exec(Action<? super ExecSpec> action);

org.gradle.process.ExecSpec
	public interface ExecSpec extends BaseExecSpec

	ExecSpec commandLine(Object... args);

org.gradle.process.BaseExecSpec
	BaseExecSpec setStandardInput(InputStream inputStream);
	InputStream getStandardInput();

	BaseExecSpec setStandardOutput(OutputStream outputStream);
	OutputStream getStandardOutput();

	List<String> getCommandLine();

org.gradle.api.initialization.dsl.ScriptHandler
	void repositories(Closure configureClosure);
	void dependencies(Closure configureClosure);

org.gradle.api.artifacts.dsl.DependencyHandler
	Dependency project(Map<String, ?> notation);

org.gradle.api.Task
	Task doFirst(Closure action);
	Task doFirst(Action<? super Task> action);
	Task doLast(Closure action);
	Task doLast(Action<? super Task> action);

	Task dependsOn(Object... paths);
	Set<Object> getDependsOn();
	void setDependsOn(Iterable<?> dependsOnTasks);
	void setDescription(String description);

	Task leftShift(Closure action);

	Task mustRunAfter(Object... paths);
	TaskDependency shouldRunAfter(Object... paths);

	void setEnabled(boolean enabled);
	boolean getEnabled();

	void onlyIf(Closure onlyIfClosure);
	

org.gradle.api.internal.AbstractTask
	public abstract class AbstractTask implements TaskInternal, DynamicObjectAware

	private List<ContextAwareTaskAction> actions;

	public void prependParallelSafeAction(final Action<? super Task> action)
	public void appendParallelSafeAction(final Action<? super Task> action)

	private ContextAwareTaskAction convertClosureToAction(Closure actionClosure)
	private ContextAwareTaskAction wrap(final Action<? super Task> action)

org.gradle.api.DefaultTask
	public class DefaultTask extends AbstractTask


org.gradle.api.tasks.TaskContainer
	public interface TaskContainer extends TaskCollection<Task>, PolymorphicDomainObjectContainer<Task>

	Task create(String name, Closure configureClosure) throws InvalidUserDataException;
	Task create(Map<String, ?> options, Closure configureClosure) throws InvalidUserDataException;

	Task findByPath(String path);
	Task getByPath(String path) throws UnknownTaskException;

org.gradle.api.tasks.TaskCollection
	public interface TaskCollection<T extends Task> extends NamedDomainObjectSet<T>

	T getAt(String name) throws UnknownTaskException;
	T getByName(String name) throws UnknownTaskException;

org.gradle.api.NamedDomainObjectSet<T>
	public interface NamedDomainObjectSet<T> extends NamedDomainObjectCollection<T>, Set<T>

org.gradle.api.NamedDomainObjectCollection<T>
	public interface NamedDomainObjectCollection<T> extends DomainObjectCollection<T>

	T findByName(String name);

	Rule addRule(String description, Closure ruleAction);
	Rule addRule(String description, Action<String> ruleAction);
	Rule addRule(Rule rule);

org.gradle.api.DomainObjectCollection<T>
	public interface DomainObjectCollection<T> extends Collection<T>

	void all(Closure action);

org.gradle.api.internal.DefaultNamedDomainObjectCollection<T>
	public class DefaultNamedDomainObjectCollection<T> extends DefaultDomainObjectCollection<T> implements NamedDomainObjectCollection<T>, MethodMixIn, PropertyMixIn
	
	private boolean applyRules(String name)

org.gradle.api.internal.tasks.DefaultTaskContainer
	public class DefaultTaskContainer extends DefaultTaskCollection<Task> implements TaskContainerInternal

org.gradle.api.plugins.PluginAware
	void apply(Closure closure);
	void apply(Action<? super ObjectConfigurationAction> action);
	void apply(Map<String, ?> options);

org.gradle.api.plugins.ObjectConfigurationAction
	ObjectConfigurationAction plugin(String pluginId);
	ObjectConfigurationAction plugin(Class<? extends Plugin> pluginClass);
	ObjectConfigurationAction from(Object script);
	

org.gradle.api.plugins.ExtraPropertiesExtension
	String EXTENSION_NAME = "ext";

org.gradle.api.plugins.BasePluginConvention
	private File libsDir;
	private File distsDir;
	private String archivesBaseName;

org.gradle.api.plugins.JavaPluginConvention
	private final SourceSetContainer sourceSets;
	public Object sourceSets(Closure closure)

	public Manifest manifest()
	public Manifest manifest(Closure closure)
	public Manifest manifest(Action<? super Manifest> action)

	public JavaVersion getTargetCompatibility()
	public void setTargetCompatibility(Object value)

	public JavaVersion getSourceCompatibility()
	public void setSourceCompatibility(Object value)

org.gradle.api.tasks.SourceSetContainer
	public interface SourceSetContainer extends NamedDomainObjectContainer<SourceSet>, NamedDomainObjectSet<SourceSet>

org.gradle.api.NamedDomainObjectContainer<T>
	public interface NamedDomainObjectContainer<T> extends NamedDomainObjectSet<T>, Configurable<NamedDomainObjectContainer<T>>

org.gradle.api.tasks.SourceSet
	String getName();
	SourceSetOutput getOutput();
	FileCollection getCompileClasspath();
	void setCompileClasspath(FileCollection classpath);

	SourceDirectorySet getJava();
	SourceSet java(Closure configureClosure);

	SourceDirectorySet getResources();
	SourceSet resources(Closure configureClosure);

org.gradle.api.file.SourceDirectorySet
	Set<File> getSrcDirs();
	SourceDirectorySet setSrcDirs(Iterable<?> srcPaths);
	SourceDirectorySet srcDirs(Object... srcPaths);
	SourceDirectorySet srcDir(Object srcPath);

org.gradle.api.tasks.SourceSetOutput
	File getClassesDir();
	void setClassesDir(File classesDir);
	File getResourcesDir();
	void setResourcesDir(Object resourcesDir);
	
org.gradle.api.artifacts.dsl.RepositoryHandler
	MavenArtifactRepository jcenter();
	MavenArtifactRepository maven(Closure closure);
	MavenArtifactRepository google();
	MavenArtifactRepository mavenCentral();
	MavenArtifactRepository mavenLocal();
	MavenArtifactRepository mavenCentral(Map<String, ?> args);

	FlatDirectoryArtifactRepository flatDir(Map<String, ?> args);
	FlatDirectoryArtifactRepository flatDir(Closure configureClosure);
	FlatDirectoryArtifactRepository flatDir(Action<? super FlatDirectoryArtifactRepository> action);

org.gradle.api.artifacts.repositories.FlatDirectoryArtifactRepository
	public interface FlatDirectoryArtifactRepository extends ArtifactRepository

	void dirs(Object... dirs);

org.gradle.api.artifacts.repositories.ArtifactRepository
	String getName();
	void setName(String name);

org.gradle.api.artifacts.Dependency
	String ARCHIVES_CONFIGURATION = "archives";

org.gradle.api.plugins.MavenRepositoryHandlerConvention
	String DEFAULT_MAVEN_DEPLOYER_NAME = "mavenDeployer";
	GroovyMavenDeployer mavenDeployer(Closure configureClosure);

org.gradle.api.publication.maven.internal.deployer.DefaultGroovyMavenDeployer
	public class DefaultGroovyMavenDeployer extends BaseMavenDeployer implements GroovyMavenDeployer, PomFilterContainer

	public RemoteRepository repository(Map properties, Closure closure)
	public RemoteRepository snapshotRepository(Map properties, Closure closure)

org.gradle.api.artifacts.maven.PomFilterContainer
	MavenPom getPom();
	void setPom(MavenPom defaultPom);

org.gradle.api.artifacts.maven.MavenPom
	String getGroupId();
	MavenPom setGroupId(String groupId);

	String getArtifactId();
	MavenPom setArtifactId(String artifactId);

	String getVersion();
	MavenPom setVersion(String version);

	String getPackaging();
	MavenPom setPackaging(String packaging);
	

org.gradle.api.publication.maven.internal.deployer.MavenRemoteRepository
	public Authentication authentication(Map properties)

----

com.android.build.gradle.AppExtension
	public class AppExtension extends TestedExtension

	private final DefaultDomainObjectSet<ApplicationVariant> applicationVariantList
	public DomainObjectSet<ApplicationVariant> getApplicationVariants()

com.android.build.gradle.TestedExtension
	public abstract class TestedExtension extends BaseExtension implements TestedAndroidConfig

	private final DomainObjectSet<TestVariant> testVariantList
	public DomainObjectSet<TestVariant> getTestVariants()

	public String getTestBuildType()
	public void setTestBuildType(String testBuildType)

com.android.build.gradle.LibraryExtension
	public class LibraryExtension extends TestedExtension

	private final DefaultDomainObjectSet<LibraryVariant> libraryVariantList
	public DefaultDomainObjectSet<LibraryVariant> getLibraryVariants()

com.android.build.gradle.BaseExtension
	public abstract class BaseExtension implements AndroidConfig
	
	private String defaultPublishConfig = "release";

	private final DefaultConfig defaultConfig;
	private final NamedDomainObjectContainer<ProductFlavor> productFlavors;
	private final NamedDomainObjectContainer<BuildType> buildTypes;
    private final NamedDomainObjectContainer<SigningConfig> signingConfigs;

	private List<String> flavorDimensionList;

	private final CompileOptions compileOptions;
	private final AdbOptions adbOptions;
	private final DexOptions dexOptions;

	private Revision buildToolsRevision;

	public void compileSdkVersion(String version)
	public void compileSdkVersion(int apiLevel)
	public void buildToolsVersion(String version)

	public void buildTypes(Action<? super NamedDomainObjectContainer<BuildType>> action)
	public void productFlavors(Action<? super NamedDomainObjectContainer<ProductFlavor>> action)
	public void signingConfigs(Action<? super NamedDomainObjectContainer<SigningConfig>> action)
	public void defaultConfig(Action<DefaultConfig> action)

	public File getDefaultProguardFile(String name)

	public void useLibrary(String name)
	public void useLibrary(String name, boolean required)

	public void compileOptions(Action<CompileOptions> action)
	public void adbOptions(Action<AdbOptions> action)
	public void dexOptions(Action<DexOptions> action)

	public void defaultPublishConfig(String value)

	public void setPublishNonDefault(boolean publishNonDefault)
	
	public void flavorDimensions(String... dimensions)

com.android.build.gradle.internal.CompileOptions
	private JavaVersion sourceCompatibility;
    private JavaVersion targetCompatibility;
	private String encoding

	public JavaVersion getSourceCompatibility()
	public void setSourceCompatibility(Object sourceCompatibility)

	public JavaVersion getTargetCompatibility()
	public void setTargetCompatibility(Object targetCompatibility)

	public String getEncoding()
	public void setEncoding(String encoding)

org.gradle.api.JavaVersion
	public enum JavaVersion

	VERSION_1_1, ..., VERSION_1_6, VERSION_1_7, VERSION_1_8, ..., VERSION_1_10

com.android.build.gradle.internal.dsl.DexOptions
	public class DexOptions extends DefaultDexOptions

com.android.builder.core.DefaultDexOptions
	public class DefaultDexOptions implements DexOptions

com.android.builder.core.DexOptions
	String getJavaMaxHeapSize();
	boolean getJumboMode();
	Integer getThreadCount();
	boolean getPreDexLibraries();

com.android.builder.model.SigningConfig
	String getName();
    File getStoreFile();
    String getStorePassword();
    String getKeyAlias();
    String getKeyPassword();
    String getStoreType();

com.android.build.gradle.internal.dsl.BuildType
	public class BuildType extends DefaultBuildType implements CoreBuildType, Serializable

	private final NdkOptions ndkConfig;
	private boolean shrinkResources = false;
	
	public void ndk(@NonNull Action<NdkOptions> action)
	public void setShrinkResources(boolean shrinkResources)
	public BuildType proguardFiles(@NonNull Object... files)
	public BuildType proguardFile(@NonNull Object proguardFile)
	
	public void buildConfigField(String type, String name, String value)

	public void resValue(String type, String name, String value)

com.android.builder.core.DefaultBuildType
	public class DefaultBuildType extends BaseConfigImpl implements BuildType

	private boolean mZipAlignEnabled = true;
	private SigningConfig mSigningConfig = null;
	private boolean mMinifyEnabled = false;

	public BuildType setSigningConfig(@Nullable SigningConfig signingConfig)
	public BuildType setZipAlignEnabled(boolean zipAlign)
	public BuildType setMinifyEnabled(boolean enabled)

com.android.builder.model.BuildType
	public interface BuildType extends BaseConfig
	
	String getName();

    boolean isMinifyEnabled();
    boolean isZipAlignEnabled();
    boolean isEmbedMicroApp();
    SigningConfig getSigningConfig();

com.android.build.gradle.internal.dsl.DefaultConfig
	public class DefaultConfig extends BaseFlavor

com.android.build.gradle.internal.dsl.BaseFlavor
	public abstract class BaseFlavor extends DefaultProductFlavor implements CoreProductFlavor

	private final NdkOptions ndkConfig;
	public void ndk(Action<NdkOptions> action)

	public void minSdkVersion(@Nullable String minSdkVersion) 
	public void minSdkVersion(int minSdkVersion)
	public void targetSdkVersion(@Nullable String targetSdkVersion)
	public void targetSdkVersion(int targetSdkVersion)

	public void proguardFile(@NonNull Object proguardFile)
	public void proguardFiles(@NonNull Object... files)

	public void buildConfigField(String type, String name, String value)

	public void resValue(String type, String name, String value)	

	public void resConfig(String config)
	public void resConfigs(String... config)

	consumerProguardFiles(Object... proguardFiles)

com.android.builder.core.DefaultProductFlavor
	public class DefaultProductFlavor extends BaseConfigImpl implements ProductFlavor

	private ApiVersion mMinSdkVersion;
	public ProductFlavor setMinSdkVersion(ApiVersion minSdkVersion)

	private ApiVersion mTargetSdkVersion;
	public ProductFlavor setTargetSdkVersion(@Nullable ApiVersion targetSdkVersion)

	private Integer mVersionCode;
	public ProductFlavor setVersionCode(Integer versionCode)

	private String mVersionName;
	public ProductFlavor setVersionName(String versionName)

    private String mApplicationId;
	public ProductFlavor setApplicationId(String applicationId)

    private String mTestApplicationId;
	public ProductFlavor setTestApplicationId(String applicationId)

    private String mTestInstrumentationRunner;
	public ProductFlavor setTestInstrumentationRunner(String testInstrumentationRunner)

	private SigningConfig mSigningConfig;
	public ProductFlavor setSigningConfig(SigningConfig signingConfig)

com.android.builder.internal.BaseConfigImpl
	public abstract class BaseConfigImpl implements Serializable, BaseConfig

	public void setManifestPlaceholders(@NonNull Map<String, Object> manifestPlaceholders)

com.android.builder.model.ProductFlavor
	public interface ProductFlavor extends BaseConfig, DimensionAware

	String getName();
	String getApplicationId();
	Integer getVersionCode();
	String getVersionName();
	ApiVersion getMinSdkVersion();
	ApiVersion getTargetSdkVersion();
	String getTestApplicationId();
	String getTestInstrumentationRunner();
	Map<String, String> getTestInstrumentationRunnerArguments();
	Boolean getTestHandleProfiling();
	Boolean getTestFunctionalTest();
	SigningConfig getSigningConfig();

com.android.builder.model.DimensionAware
	String getDimension();

com.android.builder.model.BaseConfig
	String getApplicationIdSuffix();
	Map<String, ClassField> getBuildConfigFields();
	Map<String, ClassField> getResValues();
	Collection<File> getProguardFiles();
	Collection<File> getConsumerProguardFiles();
	Map<String, Object> getManifestPlaceholders();
	Boolean getMultiDexEnabled();
	File getMultiDexKeepFile();
	File getMultiDexKeepProguard();

com.android.build.gradle.api.ApplicationVariant
	public interface ApplicationVariant extends ApkVariant, TestedVariant

com.android.build.gradle.api.ApkVariant
	public interface ApkVariant extends BaseVariant, InstallableVariant, AndroidArtifactVariant

com.android.build.gradle.api.AndroidArtifactVariant
	public interface AndroidArtifactVariant extends VersionedVariant

com.android.build.gradle.api.VersionedVariant
	int getVersionCode();
	String getVersionName();

com.android.build.gradle.internal.api.TestedVariant

com.android.build.gradle.api.LibraryVariant
	public interface LibraryVariant extends BaseVariant, TestedVariant

com.android.build.gradle.api.BaseVariant
	String getName()
	String getFlavorName()
	DomainObjectCollection<BaseVariantOutput> getOutputs()
	BuildType getBuildType()

	void buildConfigField(String type, String name, String value);

    void resValue(String type, String name, String value);

com.android.build.gradle.api.BaseVariantOutput
	public interface BaseVariantOutput extends OutputFile

com.android.build.OutputFile
	public interface OutputFile extends VariantOutput
	
	File getOutputFile();