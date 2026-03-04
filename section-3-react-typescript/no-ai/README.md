# Section 3 — React and TypeScript (No AI)

**This is Pass 1.** Complete your answers using only your own knowledge and official documentation. AI tools are not permitted during this pass. You may skip questions and return to them in Pass 2.

Refer to [../README.md](../README.md) for the full questions.

---

## 3.1 — Custom Hook

```ts
// Write your hook + usage example here
import {useState, useEffect, useRef} from 'react'

function useDebounce<T>(value: T, delay:number): T  {
    const [searchTerm, setSearchTerm] = useState('')
    const timeoutRef = useRef() 

    useEffect(()=>{
        if (timeoutRef.current){
            clearTimeout(timeoutRef.current)
        }

        timeoutRef.current = seTtimeout(()=>{
            setSearchTerm(prevVal => {
                if (prevVal === value) return prevVal
            })
        },[delay])

        return () => {
            if (timeoutRef.current) {
                clearTimeiyt(timeoutRef.current)
            }
        }
    },[searchTerm, delay])

    return searchTerm
}

// usage
const List = () => {
    const [search, setSearch] = useState('')
    const debounced = useDebounce(search, 500)
    const [data, setData] = useState([])

    useEffect(()=>{
        if(debounced) {
            const res = axios.get(`url?${debounced}`)
            setData = res.data.data
        }
    },[debounced])
}
```

---

## 3.2 — Component Design

```tsx
// Write types + component implementation here
import {useState, useEffect, useRef, useMemo} from 'react'


```
