# How to setup Jest and React Testing Library in Vite project

## 1- For little bit complex testing change App.jsx like below



    // App.jsx
    import "./App.css";
    import React from "react";
    import { useEffect, useState } from "react";
    
    function App() {
      const [todos, setTodos] = useState([]);
    
      useEffect(() => {
        fetch("https://jsonplaceholder.typicode.com/todos")
          .then((response) => response.json())
          .then((json) => setTodos(json));
      }, []);
    
      return (
        <>
          {todos.map((todo) => (
            <div key={todo.id}>
              <h1>{todo.title}</h1>
              <p>{todo.completed}</p>
            </div>
          ))}
        </>
      );
    }
    
    export default App;
	
## 2- Add App.test.js and App.snapshot.test.js files besides App.jsx file:



    // App.test.js
    import { render, screen } from "@testing-library/react";
    
    import App from "./App";
    
    describe("App", () => {
      let originalFetch;
    
      beforeEach(() => {
        originalFetch = global.fetch;
        global.fetch = jest.fn(() =>
          Promise.resolve({
            json: () =>
              Promise.resolve([
                {
                  userId: 1,
                  id: 1,
                  title: "Kaliteye hoşgeldiniz",
                  completed: false,
                },
              ]),
          })
        );
      });
    
      afterEach(() => {
        global.fetch = originalFetch;
      });
    
      it("renders App component", async () => {
        render(<App />);
        const linkElement = await screen.findByText(/Kaliteye hoşgeldiniz/i);
        expect(linkElement).toBeInTheDocument();
      });
    });
	

------------

    // App.snapshot.test.js
    import renderer from "react-test-renderer";
    
    import App from "./App";
    
    test("renders learn react link", () => {
      const tree = renderer.create(<App />).toJSON();
      expect(tree).toMatchInlineSnapshot();
    });
	
## 3- Change .eslintrc.cjs with following

    module.exports = {
      extends: ["react-app", "react-app/jest"],
    };

## 4- Add jest in package and add into package.json

`npm i jest`
------------
  

    "scripts": {
        "dev": "vite",
        "build": "vite build",
        "lint": "eslint src --ext js,jsx --report-unused-disable-directives --max-warnings 0",
        "preview": "vite preview",
        "test": "jest"
      },
  
## 5- For JSX support, add babel presets and add .babelrc to your project

`npm i @babel/preset-env @babel/preset-react`

    {
      "presets": [
        "@babel/preset-env",
        ["@babel/preset-react", { "runtime": "automatic" }]
      ]
    }

## 6- Add react testing library dependencies

`npm i @testing-library/react @testing-library/jest-dom`

## 7- To support SVG and CSS files add jest-svg-transformer and identity-obj-proxy. Then add into moduleMapper inside package.json jest config

`npm i jest-svg-transformer identity-obj-proxy`

     "jest": {
        "moduleNameMapper": {
          "^.+\\.svg$": "jest-svg-transformer",
          "^.+\\.(css|less|scss)$": "identity-obj-proxy"
        }
      }
	  
## 8- To support web environment API, install jest-environment-jsdom add into jest config:

`npm i jest-environment-jsdom`

    "jest": {
        "testEnvironment": "jsdom",
        "moduleNameMapper": {
          "^.+\\.svg$": "jest-svg-transformer",
          "^.+\\.(css|less|scss)$": "identity-obj-proxy"
        }
      }
# 9- Additionally add @testing-library/jest-dom package and configure setupTests.js

`npm i jest-environment-jsdom`

    "jest": {
        "testEnvironment": "jsdom",
        "moduleNameMapper": {
          "^.+\\.svg$": "jest-svg-transformer",
          "^.+\\.(css|less|scss)$": "identity-obj-proxy"
        },
        "setupFilesAfterEnv": [
          "<rootDir>/setupTests.js"
        ]
      }
------------
    // setupTests.js
    import "@testing-library/jest-dom";
	
## 10- And finally for snapshot testing add react-test-renderer

`npm i react-test-renderer`

## 11- You can start tests with yarn test now

`npm run test`
