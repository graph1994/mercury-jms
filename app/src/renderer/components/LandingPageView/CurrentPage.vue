<template>

  <div>
    <h2>Connection</h2>
    <div class="">
      <span>Host</span>
      <input type="text" name="" value="" v-model="connection.host" :placeholder="placeholderValues.host">
    </div>
    <div class="">
      <span>Port</span>
      <input type="text" name="" value="" v-model="connection.port" :placeholder="placeholderValues.port">
    </div>
    <div class="">
      <span>Login Username</span>
      <input type="text" name="" value="" v-model="connection.login" :placeholder="placeholderValues.login">
    </div>
    <div class="">
      <span>Login Password</span>
      <input type="text" name="" value="" v-model="connection.passcode" :placeholder="placeholderValues.passcode">
    </div>
    <div class="">
      <span>Topic or Queue</span>
      <select class="" name="" v-model="connection.type">
        <option value="/queue">Queue</option>
        <option value="/topic">Topic</option>
      </select>
    </div>
    <div>
      <span>Endpoint Name</span>
      <input type="text" name="" value="" v-model="connection.endpoint" :placeholder="placeholderValues.endpoint">
    </div>
    <div v-for="header in connection.headerValues" class="">
        <span>Header Name</span>
        <input type="text" name="" value="" v-model="header.headerName">
        <span>Header Value</span>
        <input type="text" name="" value="" v-model="header.headerValue">
        <span class="remove-btn" @click="removeHeader(header.headerValue)"> - </span>
    </div>
    <div class="add-btn" @click="addHeader">
      + Header Field
    </div>
    <div class="">
      <span>Message Body</span>
      <textarea name="name" rows="4" cols="30" v-model="connection.msgBody" :placeholder="placeholderValues.msgBody"></textarea>
    </div>
    <button type="button" name="button" @click="createConnection">Send</button>
    <b-alert :show="error != null" state="danger">
      Error Connecting: {{ error }}
    </b-alert>
  </div>
</template>

<script>
  const stompit = require('stompit');
  export default {
    data() {
      return {
        error: null,
        connection: {
          headerValues: [{}],
        },
        placeholderValues: {
          host: 'localhost',
          port: '61613',
          login: 'admin',
          passcode: 'admin',
          endpoint: 'test',
          msgBody: '{"message": "test"}',
        },
      };
    },
    created() {
      // Set $route values that are not preset during unit testing
      if (process.env.NODE_ENV === 'testing') {
        this.$route = {
          name: 'landing-page',
          path: '/landing-page',
        };
      }
    },
    methods: {
      addHeader() {
        this.connection.headerValues.push({});
      },
      removeHeader(headerVal) {
        this.connection.headerValues.splice(this.connection.headerValues.indexOf(headerVal), 1);
      },
      /* eslint-disable */
      createConnection() {
        const c = this.connection;
        const placeholder = this.placeholderValues;
        // Build destination string
        const destination = `/${c.type}/${c.endpoint}`;
        const connectOptions = {
          host: (c.host || placeholder.host),
          port: (c.port || placeholder.port),
          connectHeaders: {
            host: '/',
            login: (c.login || placeholder.login),
            passcode: (c.passcode || placeholder.passcode),
            'heart-beat': '5000,5000',
          },
        };

        stompit.connect(connectOptions, (error, client) => {
          if (error) {

            console.log('connect error ' + error.message);
            this.error = error.message;
            return;
          }

          const headerName = (c.headerName || 'messageType');
          const msgBody = (c.msgBody || placeholder.msgBody);
        	const sendHeaders = {
            //default header fields
        	    'destination': destination || placeholder.endpoint,
        	    'content-type': 'text/plain',
        	};
          // Add custom headers before sending
          for(item in c.headerValues) {
            c.headerValues[item.headerName] = item.headerValues;
          }
        	var frame = client.send(sendHeaders);
        	frame.write(msgBody);
        	frame.end();
        	var subscribeHeaders = {
        	    'destination': destination ,
        	    'ack': 'client-individual'
        	};

        	// client.subscribe(subscribeHeaders, function(error, message) {
          //
        	// 	if (error) {
        	// 	    console.log('subscribe error ' + error.message);
        	// 	    return;
        	// 	}
          //
        	// 	message.readString('utf-8', function(error, body) {
          //
        	// 		if (error) {
        	// 		    console.log('read message error ' + error.message);
        	// 		    return;
        	// 		}
          //
        	// 		console.log('received message: ' + body);
          //
        	// 		client.ack(message);
          //
        	// 		client.disconnect();
        	// 	    });
        	//     });
            });
      },
      /* eslint-enable */
    },
  };
</script>

<style scoped>
  code {
    background-color: rgba(40, 56, 76, .5);
    border-radius: 3px;
    color: #fff;
    font-weight: bold;
    padding: 3px 6px;
    margin: 0 3px;
    vertical-align: bottom;
  }
  .remove-btn {
    font-size: 24pt;
    color: red;
  }
  .add-btn {
    font-size: 24pt;
    color: green;
  }
  p { line-height: 24px; }
</style>
