TypeError: Cannot read properties of null (reading 'querySelector')
Bootstrap
C:/Applications/react/deneme/node_modules/ykb-shell/lib/bootstrap.js:100
   97 | });
   98 | var sparxEl = document.getElementById(mountId);
   99 | if (!sparxEl) return;
> 100 | var mountEl = process.env.NODE_ENV === 'development' && standAloneMode === true ? sparxEl : sparxEl.shadowRoot.querySelector('.sparx-app-container');
      | ^  101 | _reactDom.default.render( /*#__PURE__*/_react.default.createElement(_ykbUi.StyleSheetManager, {
  102 |   target: mountEl
  103 | }, /*#__PURE__*/_react.default.createElement(_ykbUi.ThemeProvider, {
View compiled
./src/index.js
C:/Applications/react/deneme/src/index.js:5
  2 | import routes from './routes';
  3 | import Bootstrap from 'ykb-shell';
  4 | 
> 5 | Bootstrap(routes);
  6 |   
View compiled
__webpack_require__
C:/Applications/react/deneme/webpack/bootstrap:853
  850 | };
  851 | 
  852 | // Execute the module function
> 853 | modules[moduleId].call(module.exports, module, module.exports, hotCreateRequire(moduleId));
      | ^  854 | 
  855 | // Flag the module as loaded
  856 | module.l = true;
View compiled
fn
C:/Applications/react/deneme/webpack/bootstrap:150
  147 | 		);
  148 | 		hotCurrentParents = [];
  149 | 	}
> 150 | 	return __webpack_require__(request);
      | ^  151 | };
  152 | var ObjectFactory = function ObjectFactory(name) {
  153 | 	return {
View compiled
1
http://localhost:3000/static/js/main.chunk.js:117:18
__webpack_require__
C:/Applications/react/deneme/webpack/bootstrap:853
  850 | };
  851 | 
  852 | // Execute the module function
> 853 | modules[moduleId].call(module.exports, module, module.exports, hotCreateRequire(moduleId));
      | ^  854 | 
  855 | // Flag the module as loaded
  856 | module.l = true;
View compiled
checkDeferredModules
C:/Applications/react/deneme/webpack/bootstrap:45
  42 | 	}
  43 | 	if(fulfilled) {
  44 | 		deferredModules.splice(i--, 1);
> 45 | 		result = __webpack_require__(__webpack_require__.s = deferredModule[0]);
     | ^  46 | 	}
  47 | }
  48 | 
View compiled
Array.webpackJsonpCallback [as push]
C:/Applications/react/deneme/webpack/bootstrap:32
  29 | 	deferredModules.push.apply(deferredModules, executeModules || []);
  30 | 
  31 | 	// run deferred modules when all chunks ready
> 32 | 	return checkDeferredModules();
     | ^  33 | };
  34 | function checkDeferredModules() {
  35 | 	var result;
View compiled
(anonymous function)
http://localhost:3000/static/js/main.chunk.js:1:89
