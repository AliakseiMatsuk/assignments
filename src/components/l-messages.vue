<template>
  <ul class="l-messages pa-0">
    <!-- ToDo: index should be replaced by id -->
    <li v-for="(message, index) in messages"
        :key="index"
        :class="message.owner"
        class="l-messages__item"
    >
      <span class="l-messages__item-inner">
        {{ message.text }}
      </span>
    </li>
  </ul>
</template>

<script>
  export default {
    name: 'l-messages',
    props: {
      messages: {
        type: Array,
        required: true,
      }
    }
  }
</script>

<style lang="scss">
  .l-messages {
    font-family: Helvetica, Arial, sans-serif;
    list-style-type: none;

    &__item {
      display: flex;

      &:not(:last-child) {
        margin-bottom: 2px;
      }

      &._bot {
        .l-messages__item-inner {
          background: #EEEEEE;
        }

        + .l-messages__item._me {
          .l-messages__item-inner {
            border-bottom-right-radius: 5px;
          }
        }
      }

      &._me {
        justify-content: flex-end;

        .l-messages__item-inner {
          background: #0084ff;
          color: #FFFFFF;
        }
      }

      &-inner {
        padding: 20px;
        border-radius: 30px;
      }
    }
  }

</style>