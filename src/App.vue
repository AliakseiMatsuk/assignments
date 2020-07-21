<template>
  <v-app>
    <v-app-bar app dark color="primary">
      <v-toolbar-title>Chatbot</v-toolbar-title>
    </v-app-bar>
    <v-main>
      <v-container class="fill-height align-stretch">
        <main class="s-main">
          <div class="s-main__messages" ref="messagesWrapper">
            <l-messages :messages="messagesToChat"/>
          </div>
          <form class="s-main__form pt-3 mt-auto" @submit.prevent="sendMessage()">
            <div class="s-main__form-field">
              <v-textarea
                      v-model="userMessage"
                      :disabled="isPossibleToTypeMessage"
                      filled
                      hide-details
                      label="Type your message"
                      @keydown.meta.enter="sendMessage()"
              />
            </div>
            <div class="s-main__form-button mt-3">
              <v-btn :disabled="isPossibleToSendMessage"
                     type="submit"
                     x-large
                     color="primary"
              >
                {{ !currentMessageIndex ? 'Let\'s chat' : 'Send Message' }}
              </v-btn>
            </div>
          </form>
        </main>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
  import axios from "axios";
  import LMessages from "@/components/l-messages";

  export default {
    name: 'App',
    components: { LMessages },
    data() {
      return {
        messagesToChat: [],
        currentMessageIndex: 0,
        messages: [],
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
    watch: {
      messagesToChat() {
        this.$nextTick(function () {
          const messagesWrapper = this.$refs.messagesWrapper;

          messagesWrapper.scrollTop = messagesWrapper.scrollHeight;
        });
      }
    },
    created() {
      axios.get('messages.json')
        .then(({ data }) => data.messages)
        .then((messages) => {
          this.messages = messages.map((msg) => ({ ...msg, "owner": "_bot" }))
        })
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
          owner: '_me'
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

<style lang="scss">
  .s-main {
    width: 100%;
    display: flex;
    flex-direction: column;

    &__messages {
      flex-grow: 1;
      display: flex;
      flex-direction: column;
      max-height: calc(100vh - 64px - 24px - 240px); // header - container padding - form height
      overflow-y: scroll;
    }

    &__form {
      flex-shrink: 0;

      &-button {
        display: flex;
        justify-content: flex-end;
        align-items: center;
      }
    }
  }
</style>
