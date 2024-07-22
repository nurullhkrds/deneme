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
