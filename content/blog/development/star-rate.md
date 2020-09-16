---
title: "별점 평가 컴포넌트"
date: 2019-02-28 10:00:00
category: development
thumbnail: { thumbnailSrc }
draft: false
---

[별점 평가 컴포넌트](https://github.com/juunone/star-rate)에 가면 데모버전 확인 가능합니다.

---

## 목적

- 영화를 본 후 평점 별점 반개도 주기 아까울때.
- 웹 & 모바일 모두 사용 가능해야 한다.

위 두가지 목적을 가지고 제작된 컴포넌트 입니다.

**시간적 제한을 가지고 제작되어서, 코드내 잡음 주의!**

## implementation

```
yarn create react-app star-rate

# project tree
- public
  - favicon.ico
  - index.html
- src
  - images
    - reset.svg
    - star-empty.svg
    - star-full.svg
    - star-half.svg
  - App.js
  - StarRate.js
  - StarRate.css
  - index.css
  - index.js
- .gitignore
- README.md
- package.json
- yarn.lock
```

## StarRate.css

- img 태그에 css를 통해 스타일을 변경해주었다.
- 모든 이미지 파일은 svg로 element의 클래스로 background가 핸들링 된다.

```css

.star__rate{
    background: url('./images/star-empty.svg');
    width: 32px;
    height: 31px;
    background-size: 100%;
    display: inline-block;
    vertical-align: -7px;
    margin:0 2px;
    cursor: pointer;
}

.star__rate.is-selected{
    background: url('./images/star-full.svg');
    width: 32px;
    height: 31px;
    background-size: 100%;
    display: inline-block;
    vertical-align: -7px;
    margin:0 2px;
    cursor: pointer;
}

.star__rate.is-half-selected{
    background: url('./images/star-half.svg');
    width: 32px;
    height: 31px;
    background-size: 100%;
    display: inline-block;
    vertical-align: -7px;
    margin:0 2px;
    cursor: pointer;

```

## StarRate.js

- init rating,idx & cache rating,idx 를 통해 상태관리가 이행된다.
- makeStars 는 dumb components로 props 를 통해 상태가 변경되면 리렌더링을 일으킨다.
- cacheRating < rating 우선순위를 가진다.
  - 마우스 오버시 현재 선택가능한 별점에 대한 부분과 수정을 보여주기 위해 저장된 별점이 아닌 마우스 오버시점의 별점에 대한 idx,rating 이 우선순위를 가진다.
- 별점은 총 0.5/1/1.5/2/2.5/3/3.5/4/4.5/5 로 배점 가능하다. 초기화시 0점
- onMouseLeave 이벤트시 현재 기존 선택된 별점있을시 배점

```javascript

  _resetRating(e) {
    if (e.type === "mouseleave" || e.type === "onTouchEnd") {
      this.props.onChange(this.props.cacheIdx, this.props.cacheRating);
    } else if (e.type === "click") {
      this.props.onChange(0, 0);
    }
  }

  _makeStars() {
    let stars = [];
    for (let i = 0; i < 5; i++) {
      let starClass = "star__rate";

      if (this.props.rating !== 0) {
        if (i <= this.props.idx) {
          if (this.props.idx === i && this.props.rating % 2 !== 0) {
            starClass += " is-half-selected";
          } else {
            starClass += " is-selected";
          }
        }
      } else if (this.props.cacheRating !== 0) {
        if (i <= this.props.cacheIdx) {
          if (this.props.cacheIdx === i && this.props.cacheRating % 2 !== 0) {
            starClass += " is-half-selected";
          } else {
            starClass += " is-selected";
          }
        }
      }

      stars.push(
        <label
          key={i}
          className={starClass}
          onClick={() => {
            this.props.onChange(this.props.idx, this.props.rating);
          }}
          onMouseOver={e => {
            this.props._mouseOver(e, i);
          }}
          onMouseMove={e => {
            this.props._mouseOver(e, i);
          }}
          onMouseLeave={e => {
            this._resetRating(e);
          }}
          onTouchMove={e => {
            this.props._mouseOver(e, i);
          }}
          onTouchStart={e => {
            this.props._mouseOver(e, i);
          }}
          onTouchEnd={e => {
            this._resetRating(e);
          }}
        />
      );
    }
    return stars;
  }

  render() {
        return (
            <div className="starRate__wrap">
                {this._makeStars()}
                <div className="reset__btn">
                    <img src={Reset} alt="reset" onClick={(e)=>{this._resetRating(e)}} />
                </div>
            </div>
        );
    }
```

## App.js

- 별점 마우스오버 비동기적인 동작수행의 Event Pooling 방지 하고 동작 수행후 데이터를 보존할수 있게 event.persist(); 사용.
- 마우스오버시 offsetX = 이벤트 대상 객체에서의 상대적 마우스 x좌표 위치 > clientWidth = 해당 엘리먼트 width / 2로 마우스의 현재위치값 계산

```javascript
  state = {
    idx: 0,
    rating: 0,
    cacheIdx: 0,
    cacheRating: 0
  };

  _mouseOver = (e, i) => {
    e.persist();
    let offsetX = e.nativeEvent.offsetX;
    let clientX = e.target.clientWidth;

    if (offsetX > clientX / 2) {
      let value = 2;
      this.setState({
        idx: i,
        rating: value
      });
    } else {
      let value = 1;
      this.setState({
        idx: i,
        rating: value
      });
    }
  };

  handleChange = (i, v) => {
    this.setState({
      idx: 0,
      rating: 0,
      cacheIdx: i,
      cacheRating: v
    });
  };

  render() {
    return (
      <StarRate
        _mouseOver={this._mouseOver}
        onChange={this.handleChange}
        idx={this.state.idx}
        rating={this.state.rating}
        cacheIdx={this.state.cacheIdx}
        cacheRating={this.state.cacheRating}
      />
    );
  }
```

## 개선해야할점

- class 핸들링이 아닌 svg 직접 제어를 통한 이미지 교체가 필요.
- 별점간의 간격 사이 onMouseLeave 이벤트 발생시 노출 개선.
