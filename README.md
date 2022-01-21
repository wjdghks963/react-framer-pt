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

### drag

원하는 component에 drag기능을 넣고 싶다면 그냥 property에 drag를 추가하기만 된다.

```javascript
<Box drag dragSnapToOrigin dragElastic={1} />
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

#### MotionValue

MotionValue(애니메이션이 실행될때)가 업데이트될 때 React Rendering Cycle을 실행하지않는다 == ReactJs State로 살지 않는다.

MotionValue는 특정한 값을 계속 추적할 수 있도록 해준다. x,y 등...

```javascript
const x = useMotionValue(0);
useEffect(() => {
  x.onChange(() => console.log(x.get()));
}, [x]);
return (
  <>
    <button onClick={() => x.set(200)}>click</button>
    <Box style={{ x }} drag="x" />
  </>
);
```

MotionValue()의 인자값은 default 값 설정이다.
특정값(위 코드의 첫번째 줄 x)을 만들고 그 값을 style에 넣으면 유저가 드래그할 때 값이 업데이트 된다.

1. 위와 같은 코드일때는 x값이 업데이트 될때 마다 x의 값을 콘솔에 찍어준다.
2. button을 누르면 x의 좌표가 200으로 움직인다.

#### useTransForm

useTransForm(x,[입력값],[출력값])

값을 하나 받고 기준으로 입력값을 입력하고 출력값을 출력한다.

```javascript
function App() {
  const x = useMotionValue(0);
  const scale = useTransform(x, [-800, 0, 800], [2, 1, 0.1]);

  return (
    <Wrapper>
      <button onClick={() => x.set(200)}>click</button>
      <Box style={{ x, scale }} drag="x" dragSnapToOrigin />
    </Wrapper>
  );
}
```

#### useViewportScroll

scrollY
페이지의 Y값

scrollYProgress
페이지의 Y축의 %

```javascript
const x = useMotionValue(0);
const { scrollYProgress } = useViewportScroll();
const scale = useTransform(scrollYProgress, [0, 1], [1, 5]);
return (
  <Wrapper style={{ background: gradient }}>
    <Box style={{ scale }} drag="x" dragSnapToOrigin />
  </Wrapper>
);
```

### SVG를 이용한 애니메이션

svg tag내에 path가 존재하고 fill element가 존재하는데 이것은 svg의 색을 결정한다.

stroke는 svg의 테두리로 색과 두께를 설정할 수 있다.
transition에서 default와 fill(duration, delay)을 통해 property를 개별로 설정할 수 있다.

```
const Svg = styled.svg`
  width: 300px;
  height: 300px;
  path {
    stroke: white;
    stroke-width: 2;
  }
`;

const svg = {
  start: { pathLength: 0, fill: "rgba(255, 255, 255, 0)" },
  end: {
    fill: "rgba(255, 255, 255, 1)",
    pathLength: 1,
  },
};

function App() {
  return (
    <Wrapper>
      <Svg
        focusable="false"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 448 512"
      >
        <motion.path
          variants={svg}
          initial={"start"}
          animate={"end"}
          transition={{
            default: {
              duration: 5,
            },
            fill: { duration: 2, delay: 5 },
          }}
          d="~~"
        ></motion.path>
      </Svg>
    </Wrapper>
  );
}
```

### Animate Presence

component이며 React.js App에서 사라지는 component를 animate한다.

규칙

1. visible한 상태
2. component안에 조건문이 존재해야한다.

```javascript
<Wrapper>
  <button onClick={toggleShowing}>Click</button>
  <AnimatePresence>
    {showing ? (
      <Box
        variants={boxVariants}
        initial="initial"
        animate="visible"
        exit="leaving"
      />
    ) : null}
  </AnimatePresence>
</Wrapper>
```

exit는 해당하는 element가 사라질때 어떤 animation을 발생시킬지 정함
