  var sparxEl = document.getElementById(mountId);
  if (!sparxEl) {
    console.error('Element with the given ID not found');
    return;
  }

  var mountEl = sparxEl;

  // Check if mountEl is found
  if (!mountEl) {
    console.error('mountEl is not found');
    return;
  }

  ReactDOM.render(
      /*#__PURE__*/React.createElement(StyleSheetManager, {
          target: mountEl
      }, /*#__PURE__*/React.createElement(ThemeProvider, {
          tokens: {
              ..._ykbDesignTokensSparx,
              shell: {
                  bgColor: _ykbDesignTokensSparx.color.primary6,
                  headerColor: '#fff',
                  headerHeight: '80px'
              }
          },
          globals: true
      }, /*#__PURE__*/React.createElement(RouteCreator, {
          container: container,
          routes: routes,
          uid: mountId,
          standAloneMode: standAloneMode,
          target: mountEl
      }))),
      mountEl
  );
}
