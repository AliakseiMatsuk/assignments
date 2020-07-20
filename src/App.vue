<template>
  <v-app>
    <v-app-bar app dark color="primary">
      <v-toolbar-title>Chatbot</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <v-container class="fill-height" fluid>
        <v-row>
          <v-col>
            <ul class="pa-0">
              <li v-for="(message, index) in messagesToChat"
                  :key="index">
                <span :class="message.owner">
                  {{ message.text }}
                </span>
              </li>
            </ul>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-textarea
                    v-model="userMessage"
                    :disabled="isPossibleToTypeMessage"
                    filled
                    hide-details
                    label="Type your message"
            ></v-textarea>
          </v-col>
        </v-row>
        <v-row>
          <v-col>
            <v-btn :disabled="isPossibleToSendMessage"
                   x-large
                   color="primary"
                   @click="sendMessage()"
                   @keyup.meta.enter="sendMessage()"
            >
              {{ !currentMessageIndex ? 'Let\'s chat' : 'Send Message' }}
            </v-btn>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
  export default {
    name: 'App',
    data() {
      return {
        messagesToChat: [],
        currentMessageIndex: 0,
        messages: [
          {
            text: "Hi, I'm Peter!",
            owner: 'him'
          },
          {
            text: "What's your name?",
            ask: "name",
            owner: 'him'
          },
          {
            text: "Nice to meet you!",
            owner: 'him'
          },
          {
            text: "How was your day?",
            ask: "feeling",
            owner: 'him'
          },
          {
            text: "Where're you from?",
            ask: "location",
            owner: 'him'
          },
          {
            text: "Nice!",
            owner: 'him'
          },
          {
            text: "How old are you?",
            ask: "age",
            owner: 'him'
          },
          {
            text: "What's your favorite hobby?",
            ask: "hobby",
            owner: 'him'
          },
          {
            text: "Wow, cool",
            owner: 'him'
          }
        ],
        userMessage: '',
        userAnswers: {
          name: '',
          age: 0,
          location: '',
          feeling: '',
          hobby: '',
        },
      }
    },
    computed: {
      currentMessage() {
        return this.messages[this.currentMessageIndex];
      },
      isCurrentMessageLastOne() {
        return this.currentMessageIndex === this.messages.length - 1;
      },
      isPossibleToSendMessage() {
        // Chat hasn't been started
        // OR
        // User input is empty
        return !!this.currentMessageIndex && !this.userMessage.trim();
      },
      isPossibleToTypeMessage() {
        return !this.currentMessageIndex || this.isCurrentMessageLastOne;
      }
    },
    methods: {
      sendMessage() {
        if (this.currentMessage.ask) { // Is current message has question
          this.userMessage ? this.proceedAnswer() : this.proceedMessageToChat();
        } else if (!this.isCurrentMessageLastOne) { // If current message NOT the last one
          this.proceedMessageToChat();
          this.goToNextMessage();
        } else {
          this.proceedMessageToChat();
        }
      },
      proceedAnswer() { // Proceed user answer
        this.userAnswers[this.currentMessage.ask] = this.userMessage;
        this.messagesToChat.push({
          text: this.userMessage,
          owner: 'me'
        });
        this.userMessage = '';
        this.goToNextMessage();
      },
      goToNextMessage() {
        this.currentMessageIndex += 1;
        this.sendMessage();
      },
      proceedMessageToChat() {
        this.messagesToChat.push(this.messages[this.currentMessageIndex]);
      },
    }
  };
</script>

<style>
  ul {
    list-style: none;
    overflow-y: scroll;
  }

  ul li {
    display: flex;
    margin-bottom: 2px;
    font-family: Helvetica, Arial, sans-serif;
  }

  ul li span {
    padding: 20px;
    border-radius: 30px;
  }

  .him {
    background: #EEEEEE;
  }

  .me {
    background: #0084ff;
    color: #FFFFFF;
  }

  .him + .me {
    border-bottom-right-radius: 5px;
  }

  .me + .me {
    border-top-right-radius: 5px;
    border-bottom-right-radius: 5px;
  }

  .me:last-of-type {
    border-bottom-right-radius: 30px;
  }
</style>
