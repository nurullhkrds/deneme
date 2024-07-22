var sparxEl = document.getElementById(mountId);
if (!sparxEl) return;

var mountEl;
if (process.env.NODE_ENV === 'development' && standAloneMode === true) {
    mountEl = sparxEl;
} else if (sparxEl.shadowRoot) {
    mountEl = sparxEl.shadowRoot.querySelector('.sparx-app-container');
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
