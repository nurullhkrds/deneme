var sparxEl = document.getElementById(mountId);
if (!sparxEl) return;

var mountEl;
if (process.env.NODE_ENV === 'development' && standAloneMode === true) {
    mountEl = sparxEl;
} else if (sparxEl.shadowRoot) {
    mountEl = sparxEl.shadowRoot.querySelector('.sparx-app-containe var sparxEl = document.getElementById(mountId);
  if (!sparxEl) return;
  var mountEl = process.env.NODE_ENV === 'development' && standAloneMode === true ? sparxEl : sparxEl.shadowRoot.querySelector('.sparx-app-container');
  _reactDom.default.render( /*#__PURE__*/_react.default.createElement(_ykbUi.StyleSheetManager, {
    target: mountEl
  }, /*#__PURE__*/_react.default.createElement(_ykbUi.ThemeProvider, {
    tokens: _objectSpread(_objectSpread({}, _ykbDesignTokensSparx.default), {}, {
      shell: {
        bgColor: _ykbDesignTokensSparx.default.color.primary6,
        headerColor: '#fff',
        headerHeight: '80px'
      }
    }),
    globals: true
  }, /*#__PURE__*/_react.default.createElement(RouteCreator, {
    container: container,
    routes: routes,
    uid: mountId,
    standAloneMode: standAloneMode,
    target: mountEl
  }))), mountEl);
};r');
} else {
    // Handle the case where shadowRoot is not available
    console.error('shadowRoot is not available on the element');
    return;
}

if (!mountEl) {
    console.error('mountEl is not found');
    return;
}

_reactDom.default.render(
    /*#__PURE__*/_react.default.createElement(_ykbUi.StyleSheetManager, {
        target: mountEl
    }, /*#__PURE__*/_react.default.createElement(_ykbUi.ThemeProvider, {
        // ... other props
    }))
);
