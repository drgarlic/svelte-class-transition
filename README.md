# svelte-class-transition

`svelte-class-transition` is a very simple Svelte 3 component that allows you to do transitions using CSS classes.

You can find multiple examples with different use cases here: https://svelte-class-transition-examples.now.sh/

## Install

Using `yarn`:

```
yarn add svelte-class-transition
```

Using `npm`:

```
npm install svelte-class-transition
```

## Usage

### Implementation

Here's how it should be used:

```html
<script>
    import Transition from 'svelte-class-transition';

    export let toggle;
</script>

<Transition
    {toggle}
    transitions="transition transform"
    inTransition="ease-out duration-1000"
    inState="opacity-0 -translate-x-16"
    onState="opacity-100 translate-x-0"
    outState="opacity-0 translate-x-16"
    outTransition="ease-in duration-1000"
>
    <!-- All the classes will be applied to the slot's main element, in this case, the div below -->
    <div>
        ...
    </div>
</Transition>
```

### Props

`svelte-class-transition` has several props for different use cases. All of them are optionnal but without any, the component won't do anything.

#### `toggle`

Type: `boolean | undefined`

Default value: `undefined`

The `toggle` property is where you'll store your toggling variable. If set to `true` the `slot` will be first visible, if set to `false` it will start hidden, you should note that there is **no animation for the first state**. On change of the property, the `slot` will animate according to the other props.

There is a possiblity to not set this property:

- If the component has a parent (or grand*-parent) transition component, it will listen to its changes and react according to them. The parent (or grand*-parent) transition component will wait until all the animations are finished before being hidden.
- If the component doesn't have a parent (or grand*-parent) transition component, it will show the slot **with an animation once the DOM has fully loaded**.

#### `inState`

Type: `string`

Default value: `''`

The `inState` property is the state from which the `slot` while come from when setting the `toggle` to `true`. If you want your component to go from `opacity-0` to `opacity-100`, you should set `inState` to `opacity-0`.

#### `onState`

Type: `string`

Default value: `''`

The `onState` property is the state from which the `slot` while go to when setting the `toggle` to `true`. If you want your component to go from `opacity-0` to `opacity-100`, you should set `onState` to `opacity-100`.

#### `outState`

Type: `string`

Default value: `inState`

The `outState` property is the state from which the `slot` while go to when setting the `toggle` to `false`. If you want your component to go from `opacity-100` to `opacity-0`, you should set `outState` to `opacity-0`. If you want the same `inState` and `outState`, you can skip this prop.

#### `inTransition`

Type: `string`

Default value: `''`

The `inTransition` property is the transition the `slot` will use after `toggle` has been set to `true`. You'll want to set here the transition, the easing, the duration and eventually the delay. For example it could be: `transition ease-out duration-300`.

#### `outTransition`

Type: `string`

Default value: `inTransition`

The `outTransition` property is the transition the `slot` will use after `toggle` has been set to `false`. You'll want to set here the transition, the easing, the duration and eventually the delay. For example it could be: `transform ease-in duration-200`. If you want the same `inTransition` and `outTransition`, you can skip this prop.

#### `transitions`

Type: `string`

Default value: `''`

The `transitions` property is where you'll regroup your common classes from `inTranstion` and `outTransition`, like for example the transition property. It's totally optionnal and doesn't change anything inside the component, it's here to make your code look cleaner.

#### `offVisible`

Type: `boolean`

Default value: `false`

The `offVisible` property is where you'll define if the `slot` should be `hidden` when off or not.
