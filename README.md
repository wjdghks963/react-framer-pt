# framer-motion

애니메이션 라이브러리
component안에 props를 이용하면 쉽게 애니메이션을 만들 수 있다.
모든 animation에는 기본적으로 튕기는 것 같은 효과(transition={{type:"spring"}})를 가지고 있다.

기초

```
div가 animate하길 원한다면 아래와 같게 쓰면 안된다.

<div></div>

밑와 같이 motion으로부터 유효한 html tag를 받아 써야한다.

<motion.div></motion.div>
```

---

styled-component를 이용한 component에 motion넣기

```javascript
const Box = styled(motion.div)`
  width: 200px;
  height: 200px;
  background-color: white;
  border-radius: 10px;
  box-shadow: 0 2px 3px rgba(0, 0, 0, 0.1), 0 10px 20px rgba(0, 0, 0, 0.06);
`;
```

---

### Variants

https://www.framer.com/docs/introduction/##variants

애니메이션의 무대, initial showing hidden from 같은 것들

설정을 분리된 object로 옮김
object | property 이름은 마음대로 정함

```javascript
const myVars = {
  start: { scale: 0 },
  end: { scale: 1, rotateZ: 360, transition: { type: "spring" } },
};

function App() {
  return (
    <Wrapper>
      <Box variants={myVars} initial="start" animate="end" />
    </Wrapper>
  );
}
```

기본적으로 부모의 object 설정들을 있을 경우 자식component에게 넘겨준다.
만약 자식에게 다른 variants를 주고 싶을 경우엔 새로운 object를 만들어 준다.
하지만 이 object의 property이름들은 부모와 같아야 한다.

component에서 설정을 줄때 string으로 object내에 있는 property를 주는것 뿐아니라 {}를 통해 js를 이용할 수 있다.

```javascript
const circleVariants = {
  start: { scale: 0 },
  end: {
    scale: 2,
    transition: {
      type: "spring",
      bounce: 0.8,
    },
  },
};

function App() {
  return (
    <Wrapper>
      <Box variants={boxVariants} initial="start" animate="end">
        <Circle variants={circleVariants} />
        <Circle variants={circleVariants} />
        <Circle variants={circleVariants} />
        <Circle variants={circleVariants} />
      </Box>
    </Wrapper>
  );
}
```

#### drag

원하는 component에 drag기능을 넣고 싶다면 그냥 property에 drag를 추가하기만 된다.

```javascript
<Box drag />
```

원하는 xyz축으로만 이동시키게 하고 싶으면 drag="x"와 같이 설정한다.

dragConstraints
드래그를 통한 이동이 가능한 범위를 설정할 수 있다.

dragSnapToOrigin
상태가 true라면 드래그를 끝내면 다시 원위치로 돌아가는 기능

dragElastic
제한된 바깥을 벗어날 수 있는 이동 정도입니다.
0 : 마우스 포인터가 제한된 공간을 벗어 나더라도 집힌 elemet는 따라서 나갈 수 없다.

0을 넘어가면 박스 밖을 따라 나가지만 제한 하는 정도가 걸리는데 Elastic의 정도에 따라 설정이 가능하다.
