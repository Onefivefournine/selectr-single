<style lang="scss"
  scoped>
$GRAY: #999c9e;
$ACTIVE:#2ecc71;
.selectr-single {
  position: relative;
  &__close {
    position: absolute;
    right: 0.75rem;
    cursor: pointer;
    top: 0;
    height: 100%;
    font-style: normal;
    font-size: 1.75em;
    font-weight: bold;
    color: $GRAY;
    &:hover {
      color: rgba($GRAY, 0.75)
    }
  }
  &__input {
    text-align: left;
    &--placeholder {
      color: $GRAY;
    }
  }
  &__dropdown {
    width: 100%;
    padding: 0;
    .dropdown-item {
      padding: 0.25rem 0.75rem;
      &:active,
      &.active {
        background-color: $ACTIVE;
      }
    }
  }
}
</style>
<template>
  <div class="selectr-single">
    <button :placeholder="placeholder"
      @focus.prevent="focus"
      @blur.prevent="blur"
      :class="{
        [className + ' selectr-single__input form-control']:true,
        'selectr-single__input--placeholder': !match
      }">
      {{match ? getOptionLabel(match) : placeholder}}
    </button>
    <i class="selectr-single__close"
      v-show="match"
      @click="match=null">&times;</i>
    <div class="dropdown-menu selectr-single__dropdown"
      :class="{show:opened}">
      <button class="dropdown-item"
        v-for="option in options"
        :class="{active:match&&match[modelValue]===option[modelValue]}"
        :key="modelValue"
        @mousedown="select(option)">{{getOptionLabel(option)}}</button>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      opened: false,
      match: null
    }
  },
  props: {
    placeholder: {
      type: String,
      default: 'Select anything'
    },
    className: {
      type: String,
      default: ''
    },
    value: {
      default: null
    },
    modelValue: {
      type: String,
      default: 'value'
    },
    label: {
      type: String,
      default: 'label'
    },
    options: {
      type: Array,
      default () {
        return []
      }
    },
    getOptionLabel: {
      type: Function,
      default ( option, mutatedLabel ) {
        let currentLabel = mutatedLabel || this.label;
        if ( typeof option === 'object' ) {
          if ( currentLabel && typeof currentLabel === 'string' && option[ currentLabel ] !== undefined ) {
            return option[ currentLabel ]
          } else if ( currentLabel.includes( '.' ) ) {
            mutatedLabel = currentLabel.split( '.' )
            let mutatedOption = option[ mutatedLabel[ 0 ] ];
            mutatedLabel.shift();
            mutatedLabel = mutatedLabel.join( '.' );
            return this.getOptionLabel( mutatedOption, mutatedLabel )
          }
        }
        return option;
      }
    },

  },
  watch: {
    match( option ) {
      this.$emit( 'input', option && option[ this.modelValue ] )
    },
    value( val ) { this.pushValue( val ) }
  },
  created() {
    this.pushValue( this.value )
  },
  methods: {
    pushValue( optionVal ) {
      if (
        ( optionVal && this.match && optionVal !== this.match[ this.modelValue ] ) ||
        ( !this.match && optionVal )
      ) {
        this.match = this.options.find( ( opt ) => ( opt && opt[ this.modelValue ] === optionVal ) )
      }
    },
    select( option ) {
      this.match = option
    },
    focus() {
      this.opened = true
    },
    blur() {
      setTimeout( () => {
        this.opened = false
      } )
    }
  }
}
</script>
