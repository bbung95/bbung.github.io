---
layout: post
title: "Component"
date: 2021-09-21 19:00:00 +0900
categories: "React"
---
# Component
---

## Component란?
---
Component는 React에서 중요한 개념이다.

## State
---

React는 State라는 영역에 값을 설정하여 화면에 보여주게 된다.

```javascript
import React from "react";
import axios from "axios";
import Movie from "./Movie.js";
import "./App.css";

class App extends React.Component {
  state = {
    isLoading: true,
    movie: [],
  };

  // 비동기 async await axios는 느리다
  getMovies = async () => {
    const {
      data: {
        data: { movies },
      },
    } = await axios.get(
      "https://yts-proxy.now.sh/list_movies.json?sort_by=rating"
    );
    this.setState({ movies, isLoading: false });
  };

  componentDidMount() {
    this.getMovies();
  }

  render() {
    const { isLoading, movies } = this.state;
    return (
      <section className="container">
        {isLoading ? (
          <div className="loader">
            <span className="loader__text">Loading....</span>
          </div>
        ) : (
          <div className="movies">
            {movies.map((movie) => (
              <Movie
                key={movie.id}
                id={movie.id}
                year={movie.year}
                title={movie.title}
                summary={movie.summary}
                poster={movie.medium_cover_image}
                genres={movie.genres}
              />
            ))}
          </div>
        )}
      </section>
    );
  }
}

export default App;

```

## Life Cycle 
---

React도 Life Cycle을 가지고 있는데 Component를 생성하고 없애는 방법이다.

**Mounting** 
  
  - 페이지 로딩시 , Component 변경시

    constructure()  
    render()  
    componentDidmount() 

**UnMounting**

 - Component 변경시

    componentWillUnMount()  

**Updating** 
  
  - setState() 발생 / 내가 행동할때

     static getDerivedStateFromProps()  
    shouldComponentUpdate()  
    render()  
    getSnapshotBeforeUpdate()  
    componentDidUpdate()  

<img src="https://bbung95.github.io/public/img/react-life-cycle.png">