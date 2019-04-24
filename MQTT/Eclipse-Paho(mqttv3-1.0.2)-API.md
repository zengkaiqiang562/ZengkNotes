## org.eclipse.paho.client.mqttv3.IMqttClient ##

	public interface IMqttClient

	public void connect() throws MqttSecurityException, MqttException;
	public void connect(MqttConnectOptions options) throws MqttSecurityException, MqttException;

	public IMqttToken connectWithResult(MqttConnectOptions options) throws MqttSecurityException, MqttException;


	public void disconnect() throws MqttException;
	public void disconnect(long quiesceTimeout) throws MqttException;

	public void disconnectForcibly() throws MqttException;
	public void disconnectForcibly(long disconnectTimeout) throws MqttException;
	public void disconnectForcibly(long quiesceTimeout, long disconnectTimeout) throws MqttException;

	public void subscribe(String topicFilter) throws MqttException, MqttSecurityException;
	public void subscribe(String[] topicFilters) throws MqttException;
	public void subscribe(String topicFilter, int qos) throws MqttException;
	public void subscribe(String[] topicFilters, int[] qos) throws MqttException;


	public void unsubscribe(String topicFilter) throws MqttException;
	public void unsubscribe(String[] topicFilters) throws MqttException;


	public void publish(String topic, byte[] payload, int qos, boolean retained) 
			throws MqttException, MqttPersistenceException;

	public void publish(String topic, MqttMessage message) throws MqttException, MqttPersistenceException;


	public void setCallback(MqttCallback callback);
	
	public MqttTopic getTopic(String topic);

	public boolean isConnected();

	public String getClientId();

	public String getServerURI();

	public void close() throws MqttException;

## org.eclipse.paho.client.mqttv3.MqttClient ##

	public class MqttClient implements IMqttClient

	protected MqttAsyncClient aClient = null;  // Delegate implementation to MqttAsyncClient

	public MqttClient(String serverURI, String clientId) throws MqttException {
		this(serverURI,clientId, new MqttDefaultFilePersistence());
	}

	public MqttClient(String serverURI, String clientId, MqttClientPersistence persistence) throws MqttException {
		aClient = new MqttAsyncClient(serverURI, clientId, persistence);
	}


## org.eclipse.paho.client.mqttv3.MqttClientPersistence ##

	public interface MqttClientPersistence

	public void open(String clientId, String serverURI) throws MqttPersistenceException;

	public void close() throws MqttPersistenceException;

	public void put(String key, MqttPersistable persistable) throws MqttPersistenceException;

	public MqttPersistable get(String key) throws MqttPersistenceException;

	public void remove(String key) throws MqttPersistenceException;

	public Enumeration keys() throws MqttPersistenceException;

	public boolean containsKey(String key) throws MqttPersistenceException;

## org.eclipse.paho.client.mqttv3.persist.MemoryPersistence ##

	public class MemoryPersistence implements MqttClientPersistence

	private Hashtable data; //保存在内存中

## org.eclipse.paho.client.mqttv3.persist.MqttDefaultFilePersistence ##

	public class MqttDefaultFilePersistence implements MqttClientPersistence

	private File dataDir; //保存在本地文件中

	public MqttDefaultFilePersistence()  { 
		this(System.getProperty("user.dir"));
	}

	public MqttDefaultFilePersistence(String directory) {
		dataDir = new File(directory);
	}


## org.eclipse.paho.client.mqttv3.MqttConnectOptions ##

	public MqttConnectOptions()

	public void setPassword(char[] password)
	
	public void setUserName(String userName)

	public void setKeepAliveInterval(int keepAliveInterval)throws IllegalArgumentException

	public void setConnectionTimeout(int connectionTimeout)

	public void setCleanSession(boolean cleanSession)

	public void setWill(String topic, byte[] payload, int qos, boolean retained) {

		validateWill(topic, payload);
		this.setWill(topic, new MqttMessage(payload), qos, retained);
	}

## org.eclipse.paho.client.mqttv3.MqttMessage ##

	public MqttMessage()

	public MqttMessage(byte[] payload)

	public void setQos(int qos)

	public void setPayload(byte[] payload)

	public void setRetained(boolean retained)

## org.eclipse.paho.client.mqttv3.MqttCallback ##

	public interface MqttCallback

	public void connectionLost(Throwable cause); //与服务器失去连接时回调

	public void messageArrived(String topic, MqttMessage message) throws Exception; //从服务器收到消息时回调

	public void deliveryComplete(IMqttDeliveryToken token);
		//For QoS 0 it is called once the message has been handed to the network for delivery. 
		//For QoS 1 it is called when PUBACK is received
		//for QoS 2 when PUBCOMP is received.
		

	