# Reusability

---

## React Props

```jsx
<Button text="Click here!"/>
```

```jsx
// Button.js

import React from "react"

export default function Button(props) {
    return (
        <button>{props.text}</button>
    )
}
```

---

## React Children

```jsx
<Button>Buy now!</Button>
<Button>
  <FaMoneyBill />
  Buy now!
</Button>
```

```jsx
// Button.js

import React from "react"

export default function Button(props) {
    return (
        <button>{props.children}</button>
    )
}
```

---

## onClick even Listener

```jsx
<Button onClick={() => console.log("Logging in...")}>
  <FcGoogle />
  Log in with Google
</Button>
```

```jsx
// Button.js

import React from "react"

export default function Button(props) {
    return (
        <button onClick={props.onClick}>{props.children}</button>
    )
}
```

---

## Props Spreading

```jsx
<Button style={{color: "green"}} onClick={() => console.log("Logging in...")}>
  <FcGoogle />
  Log in with Google
</Button>
```

```jsx
// Button.js

import React from "react"

export default function Button(props) {
    return (
        <button {...props}>
            {props.children}
        </button>
    )
}
```

---

## Destructuring Props

```jsx
<Button style={{color: "green"}} onClick={() => console.log("Logging in...")}>
  <FcGoogle />
  Log in with Google
</Button>
```

```jsx
// Button.js

import React from "react"

export default function Button({children, ...rest}) {
    return (
        <button {...rest}>
            {children}
        </button>
    )
}
```

---

## Props Function

```jsx
<div>
    <Decision sayName={(goingOut) => {
        console.log(goingOut ? "I AM going out" : "I'm staying in")
    }} />
</div>
```

```jsx
// Decision.js

import React from "react"

export default function Decision({ sayName }) {
    const [goingOut, setGoingOut] = React.useState(false)
    
    sayName(goingOut)

    function toggleGoingOut() {
        setGoingOut(prev => !prev)
    }

    return (
        <div>
            <button onClick={toggleGoingOut}>Change mind</button>
            <h1>Am I going out tonight?? {goingOut ? "Yes!" : "Nope..."}</h1>
        </div>
    )
}
```

---

## Overloading Component

```jsx
<>
  <Avatar src="./images/bob.jpg" alt="Bob Ziroll" />
  <br />
  <Avatar>BZ</Avatar>
  <br />
  <Avatar />
</>
```

```jsx
// Avatar.js

import React from "react"
import { IoPersonSharp } from "react-icons/io5"

export default function Avatar({src, alt, children}) {
    if (src) {
        return (
            <div className="avatar">
                <img src={src} alt={alt}/>
            </div>
        )
    }
    if (children) {
        return (
            <div className="avatar avatar-letters">
                {children}
            </div>
        )
    }
    else {
        return (
            <div className="avatar avatar-icon">
                <IoPersonSharp />
            </div>
        )
    }
}
```

---

## React useState

```jsx
import React from "react"
import MenuButton from "./MenuButton"
import MenuDropdown from "./MenuDropdown"

export default function Menu({ buttonText = "Menu", items }) {
    const [open, setOpen] = React.useState(true)

    function toggle() {
        setOpen(prevOpen => !prevOpen)
    }

    return (
        <div className="menu">
            <MenuButton
                buttonText={buttonText}
                onClick={toggle}
            />

            {open && <MenuDropdown items={items} />}
        </div>
    )
}

```

---

## React useEffect

```jsx
const [on, setOn] = React.useState(false)

React.useEffect(() => {
    onToggle()
},
  [on] // When it changes, the onToggle will be triggered
  // Or you can empty this if want to trigger when code runned (CAUSING RE-RENDER)
)
```

NOTE: Use “useRef” to prevent re-render

---

## React useRef

Use this to prevent re-render (infinite loop on useEffect)

```jsx
const [on, setOn] = React.useState(false)
const firstRender = React.useRef(true)

React.useEffect(() => {
    if (firstRender.current) {
        firstRender.current = false
    } else {
        onToggle()
    }
}, [on])
```

---

## React useContext + useState

![Untitled](Reusability%2084a8eceaae45483ba313ee71c119abc2/Untitled.png)

On App.js, put the Provider:

```jsx
import React from "react"
import Header from "./Header"
import Button from "./Button"

const ThemeContext = React.createContext()

export default function App() {
    const [theme, setTheme] = React.useState("light")
		
    function toggleTheme() {
        setTheme(prevTheme => prevTheme === "light" ? "dark" : "light")
    }
    
    return (
        <ThemeContext.Provider value={{theme, toggleTheme}}>
            <div className={`container ${theme}-theme`}>
                <Header />
                <Button />
            </div>
        </ThemeContext.Provider>
    )
}

export { ThemeContext }
```

Then, implement the color on header:

```jsx
import React from "react"
import { ThemeContext } from "./App"

export default function Header() {
    const { theme } = React.useContext(ThemeContext)

    return (
        <header className={`${theme}-theme`}>
            <h1>{theme === "light" ? "Light" : "Dark"} Theme</h1>
        </header>
    )
}
```

And now, put the toggleTheme on Button:

```jsx
import React from "react"
import { ThemeContext } from "./App"

export default function Button() {
    const { theme, toggleTheme } = React.useContext(ThemeContext)

    return (
        <button onClick={toggleTheme} className={`${theme}-theme`}>
            Switch Theme
        </button>
    )
}
```

This is the result:

![Untitled](Reusability%2084a8eceaae45483ba313ee71c119abc2/Untitled%201.png)

![Untitled](Reusability%2084a8eceaae45483ba313ee71c119abc2/Untitled%202.png)

---

## Compound Components with Dot Syntax

Look, why the component’s name using Menu “dot” blabla?

```jsx
import Menu from "./Menu/index"

function App() {
  const sports = ["Tennis", "Pickleball", "Racquetball", "Squash"]

  return (
    <Menu>
      <Menu.Button>Sports</Menu.Button>
      <Menu.Dropdown>
        {sports.map(sport => (
          <Menu.Item key={sport}>{sport}</Menu.Item>
        ))}
      </Menu.Dropdown>
    </Menu>
  )
}
```

How?

![Untitled](Reusability%2084a8eceaae45483ba313ee71c119abc2/Untitled%203.png)

Because of this:

```jsx
// ./Menu/index.js

import Menu from "./Menu"
import MenuButton from "./MenuButton"
import MenuDropdown from "./MenuDropdown"
import MenuItem from "./MenuItem"

Menu.Button = MenuButton
Menu.Dropdown = MenuDropdown
Menu.Item = MenuItem

export default Menu
```

---

## Headless Toggle Component

```jsx
<Toggle>
  <Toggle.Button>
    <Toggle.On>
      <BsStarFill className="star filled" />
    </Toggle.On>
    <Toggle.Off>
      <BsStar className="star" />
    </Toggle.Off>
  </Toggle.Button>
</Toggle>
```

```jsx
// ./Toggle/Toggle.js

import React from "react"

const ToggleContext = React.createContext()

export default function Toggle({ children }) {
    const [on, setOn] = React.useState(false)
    
    function toggle() {
        setOn(prevOn => !prevOn)
    }
    
    return (
        <ToggleContext.Provider value={{ on, toggle }}>
            {children}
        </ToggleContext.Provider>
    )
}

export {ToggleContext}
```

```jsx
// ./Toggle/ToggleButton.js

import React from "react"
import { ToggleContext } from "./Toggle"

export default function ToggleButton({ children }) {
    const { on, toggle } = React.useContext(ToggleContext)
    return (
        <div onClick={toggle}>
            {children}
        </div>
    )
}
```

```jsx
// ./Toggle/ToggleOff.js

import React from "react"
import { ToggleContext } from "./Toggle"

export default function ToggleOn({ children }) {
    const { on } = React.useContext(ToggleContext)
    return on ? null : children
}
```

```jsx
// ./Toggle/ToggleOn.js

import React from "react"
import { ToggleContext } from "./Toggle"

export default function ToggleOn({ children }) {
    const { on } = React.useContext(ToggleContext)
    return on ? children : null
}
```

```jsx
// ./Toggle/index.js

Toggle.Button = ToggleButton
Toggle.On = ToggleOn
Toggle.Off = ToggleOff
export default Toggle
```

---

## Toggle Context

```jsx
<>
  <Toggle>
    <Star />
  </Toggle>
</>
```

```jsx
// ./Toggle/Toggle.js

import React from "react"

const ToggleContext = React.createContext()

export default function Toggle({ children }) {
    const [on, setOn] = React.useState(false)

    function toggle() {
        setOn(prevOn => !prevOn)
    }

    return (
        <ToggleContext.Provider value={{ on, toggle }}>
            {children}
        </ToggleContext.Provider>
    )
}

export { ToggleContext }
```

```jsx
// Star.js

import React from "react"
import { BsStar, BsStarFill } from "react-icons/bs"

export default function Star() {
    const [starred, setStarred] = React.useState(false)
    
    function toggle() {
        setStarred(prev => !prev)
    }
    
    return (
        starred ? 
        <BsStarFill className="star filled" onClick={toggle} /> :
        <BsStar className="star " onClick={toggle} />
    )
}
```

---

## Toggle.On & Toggle.Off

```jsx
<>
  <Toggle>
    <Toggle.Button>
      <Star />
    </Toggle.Button>
    <Toggle.On>The toggle is on</Toggle.On>
    <Toggle.Off>The toggle is off</Toggle.Off>
  </Toggle>
</>
```

```jsx
// Star.js

import React from "react"
import { BsStar, BsStarFill } from "react-icons/bs"

export default function Star() {
    const [starred, setStarred] = React.useState(false)
    
    function toggle() {
        setStarred(prev => !prev)
    }
    
    return (
        starred ? 
        <BsStarFill className="star filled" onClick={toggle} /> :
        <BsStar className="star " onClick={toggle} />
    )
}
```

```jsx
// ./Toggle/Toggle.js

import React from "react"

const ToggleContext = React.createContext()

export default function Toggle({ children }) {
    const [on, setOn] = React.useState(false)

    function toggle() {
        setOn(prevOn => !prevOn)
        console.log("Toggled")
    }

    return (
        <ToggleContext.Provider value={{ on, toggle }}>
            {children}
        </ToggleContext.Provider>
    )
}

export { ToggleContext }
```

```jsx
// ./Toggle/ToggleButton.js

import React from "react"
import { ToggleContext } from "./Toggle"

export default function ToggleButton({ children }) {
    const { toggle } = React.useContext(ToggleContext)
    
    return (
        <div onClick={toggle}>
            {children}
        </div>
    )
}
```

```jsx
// ./Toggle/ToggleOff.js

import React from "react"
import { ToggleContext } from "./Toggle"

export default function ToggleOff({ children }) {
    const { on } = React.useContext(ToggleContext)
    
    return on ? null : children
}
```

```jsx
// ./Toggle/ToggleOn.js

import React from "react"
import { ToggleContext } from "./Toggle"

export default function ToggleOn({ children }) {
    const { on } = React.useContext(ToggleContext)
    
    return on ? children : null
}
```

```jsx
// ./Toggle/index.js

import Toggle from "./Toggle"
import ToggleButton from "./ToggleButton"
import ToggleOn from "./ToggleOn"
import ToggleOff from "./ToggleOff"

Toggle.Button = ToggleButton
Toggle.On = ToggleOn
Toggle.Off = ToggleOff

export default Toggle
```

---

## Custom Hooks

![Untitled](Reusability%2084a8eceaae45483ba313ee71c119abc2/Untitled%204.png)

```jsx
useEffectOnUpdate(onToggle, [on])
```

```jsx
// ./hooks/useEffectOnUpdate.js

import React from "react"

export default function useEffectOnUpdate(effectFunction, deps) {
    const firstRender = React.useRef(true)
    
    React.useEffect(() => {
        if (firstRender.current) {
            firstRender.current = false
        } else {
            effectFunction()
        }
    }, deps)
}
```