# SuperMQTT

A comprehensive MQTT client component for Budibase applications that enables real-time communication with IoT devices, message brokers, and streaming data sources through the MQTT protocol.

## 🚀 Features

### MQTT Connectivity

- **Message Broker Connection**: Connect to any MQTT broker (localhost, cloud services, IoT hubs)
- **Authentication Support**: Username/password and client certificate authentication
- **Persistent Connections**: Automatic reconnection with configurable parameters
- **Keep Alive Management**: Configurable ping intervals and connection maintenance

### Message Handling

- **Topic Subscription**: Subscribe to specific MQTT topics for targeted messaging
- **Real-time Streaming**: Receive and process live MQTT messages
- **JSON Message Processing**: Built-in JSON parsing for structured data
- **Message Timestamps**: Track when messages were received

### Connection Management

- **Connection Status**: Real-time connection state monitoring
- **Error Handling**: Comprehensive connection error management
- **Reconnection Logic**: Automatic retry with exponential backoff
- **Status Visualization**: Optional status display component

### Event System

- **Connection Events**: onConnect and onDisconnect triggers
- **Message Events**: onMessage events with full message context
- **Global Context**: Share connection state and messages across app
- **Event Integration**: Full integration with Budibase automations

### Testing & Debugging

- **Test Panel**: Built-in message sending and topic testing interface
- **Status Monitoring**: Real-time connection and message status display
- **Message Inspection**: View incoming message structure and content
- **Debug Tools**: Comprehensive debugging capabilities

## 🎯 Usage Instructions

### Basic MQTT Connection

1. Add the SuperMQTT component to your screen
2. Configure broker connection (host, port, authentication)
3. Set topic subscription for incoming messages
4. Configure JSON parsing if needed
5. Handle onMessage events for data processing

### Broker Configuration

#### Standard MQTT Broker

- **Host**: MQTT broker hostname or IP address
- **Port**: Connection port (default 8083 for websockets)
- **Client ID**: Unique client identifier (auto-generated if empty)
- **Topic**: Subscribe to specific MQTT topic patterns

#### Secure Connection

- **Username**: Authentication username
- **Password**: Authentication password
- **SSL/TLS**: Automatic secure connection support
- **Certificate Authentication**: Client certificate support

### Message Processing

#### Real-time Data Processing

- **JSON Message**: Enable for structured data parsing
- **Message Events**: Configure actions to run on message receipt
- **Topic Filtering**: Subscribe to specific message topics
- **Data Transformation**: Process and transform incoming data

#### Status Monitoring

- **Connection State**: Monitor connection status in real time
- **Message Timestamps**: Track when messages were received
- **Error States**: Handle connection disruptions gracefully
- **Debug Information**: View detailed connection and message logs

### Testing Setup

#### Test Panel Configuration

- **Show Test Panel**: Enable message sending for debugging
- **Topic Publishing**: Send test messages to broker
- **Message Formatting**: Send JSON or plain text messages
- **Connection Verification**: Test publish/subscribe operations

### Advanced Configuration

#### Connection Maintenance

- **Keep Alive**: Configure ping intervals (default 60 seconds)
- **Max Reconnect**: Set maximum reconnection attempts
- **Reconnection Strategy**: Automatic reconnection on failure
- **Connection Quality**: Monitor connection reliability

#### Message Processing

- **Topic Patterns**: Use wildcards for topic subscriptions
- **QoS Levels**: Message quality of service settings
- **Retain Messages**: Handle retained message processing
- **Large Payloads**: Support for large message sizes

## 🔧 Configuration Properties

### Connection Settings

- **Host**: MQTT broker hostname or IP address (default: localhost)
- **Port**: Connection port (default: 8083)
- **Client ID**: Unique client identifier (auto-generated if empty)
- **Username**: Optional authentication username
- **Password**: Optional authentication password

### Topic Settings

- **Topic**: MQTT topic to subscribe to (default: test)
- **JSON Message**: Enable JSON message parsing
- **Message Context**: Access message content and metadata

### Connection Management

- **Keep Alive**: Ping interval in seconds (default: 60)
- **Max Reconnect**: Maximum reconnection attempts (default: 10)
- **Status Display**: Show/hide connection status
- **Test Panel**: Enable/disable debugging interface

### Event Configuration

- **onConnect**: Trigger when connection established
- **onDisconnect**: Trigger when connection lost
- **onMessage**: Trigger when message received, with message context

## 📋 Usage Examples

### IoT Device Monitoring

Monitor sensor data from MQTT-connected devices:

- Host: `mqtt.broker.address` (external broker)
- Topic: `sensors/#` (wildcard subscription)
- JSON Message: Enabled
- onMessage: Store sensor data for dashboard display

### Real-time Notifications

Display live notifications from messaging systems:

- Host: `notifications.internal` (internal broker)
- Topic: `app/notifications`
- JSON Message: Disabled
- Show Status: Enabled for debugging

### Asset Tracking System

Track location and status of mobile assets:

- Host: `fleet.broker.com`
- Topic: `fleets/+/location` (pattern matching)
- Client ID: `dashboard-client`
- Keep Alive: 30 seconds for mobile connections

### Industrial Equipment Monitoring

Monitor factory equipment with high reliability:

- Host: `factory.mqtt.local`
- Topic: `equipment/#/status`
- Max Reconnect: 20 (high reliability requirement)
- Username/Password: Enabled for secure access
- onConnect: Send registration message

### Smart Home Integration

Control and monitor IoT home devices:

- Host: `home.automation` (setup home MQTT broker)
- Topic: `house/#` (all house devices)
- JSON Message: Enabled for device state
- Show Test Panel: Enabled for device testing

### Data Pipeline Integration

Feed incoming MQTT data into processing workflows:

- Host: `data.ingest.broker`
- Topic: `benchmarks/*/results`
- JSON Message: Enabled
- onMessage: Trigger data processing automation

## 🎨 Global Context Variables

The SuperMQTT component provides these context variables:

- **`connected`** (`boolean`): Current connection state to MQTT broker
- **`status`** (`string`): Detailed connection status information
- **`lastMessage`**: Content of the most recent MQTT message received
- **`at`**: Timestamp when the last message was received

These variables are accessible throughout your application using binding syntax.

## 🔗 Integration & Automation

### Budibase Automation Triggers

- **Message-based Actions**: Execute automations when MQTT messages arrive
- **Connection State Changes**: Trigger workflows on connection status changes
- **Data Processing**: Automatic processing of MQTT payload data
- **Notification Systems**: Real-time notifications based on MQTT events

### Dashboard Integration

- **Real-time Dashboards**: Update dashboard components when messages arrive
- **Status Indicators**: Display connection and device status information
- **Data Visualization**: Show MQTT data in charts and graphs
- **Control Interfaces**: Send control messages using test panel or automations

### Data Source Integration

- **External Data Sources**: Pull data from MQTT into Budibase datasources
- **External API Integration**: Bridge MQTT data to REST APIs
- **Database Updates**: Store MQTT messages in Budibase tables
- **Cross-system Synchronization**: Sync data between MQTT systems and databases

### Security Integration

- **Secure Connections**: TLS/SSL encryption for MQTT connections
- **Authentication**: Username/password and certificate authentication
- **Topic Permissions**: Control access to specific MQTT topics
- **Audit Logging**: Track message transactions for compliance

## 📊 Real-time Data Processing

### Stream Processing

- **Message Filtering**: Process only relevant messages based on content
- **Data Aggregation**: Combine multiple MQTT messages for dashboard display
- **State Management**: Maintain application state from MQTT message data
- **Time-based Operations**: Handle time-ordered MQTT message streams

### Performance Optimization

- **Message Throttling**: Control processing speed for high-frequency messages
- **Connection Pooling**: Efficient connection management for multiple MQTT brokers
- **Caching Strategies**: Cache frequently accessed MQTT data
- **Background Processing**: Queue message processing for optimal performance

### Error Handling

- **Connection Failures**: Graceful handling of network interruptions
- **Message Processing Errors**: Comprehensive error handling for malformed messages
- **Broker Disconnections**: Automatic reconnection with jitter
- **Message Loss**: Detect and handle dropped MQTT messages

## 🤝 MQTT Protocol Features

### Quality of Service

- **QoS 0 (At Most Once)**: Fire-and-forget message delivery
- **QoS 1 (At Least Once)**: Guaranteed delivery with possible duplicates
- **QoS 2 (Exactly Once)**: Strict ordering and delivery guarantees

### Topic Management

- **Wildcard Subscriptions**: Support for `+` and `#` wildcards in topics
- **Topic Filtering**: Subscribe to specific message streams
- **Dynamic Subscriptions**: Change topics dynamically based on application state
- **Permissions Management**: Topic-based access control

### Retained Messages

- **Last Known State**: Access retained messages for current state
- **Initial State**: Ensure dashboard shows latest data on load
- **State Synchronization**: Maintain consistent view across all clients
- **Data Persistence**: Retain critical state information on MQTT broker

## 🐛 Troubleshooting

### Connection Issues

- **Network Connectivity**: Verify broker hostname and port are accessible
- **Firewall Configuration**: Ensure MQTT ports are open
- **Authentication Errors**: Validate username and password credentials
- **Certificate Issues**: Check SSL/TLS certificate configuration

### Message Delivery Problems

- **Topic Matching**: Verify subscription topics match published topics
- **QoS Settings**: Check quality of service compatibility
- **Message Size**: Ensure messages aren't too large for broker limits
- **Permissions**: Verify user has permission to subscribe/publish

### Performance Issues

- **High Message Rates**: Implement message throttling for very active topics
- **Connection Limits**: Monitor broker connection limits
- **Client Load**: Optimize message processing performance
- **Network Latency**: Monitor for network-related delays

### Debugging Techniques

- **Test Panel Usage**: Use built-in test panel for message verification
- **Connection Logging**: Enable detailed connection logging
- **Message Inspection**: View message payload and metadata
- **Status Monitoring**: Track connection state and message flow

Find out more about developing [Custom Plugins](https://docs.budibase.com/docs/custom-plugin) and [Budibase](https://github.com/Budibase/budibase).
