  const returnMapDefinitionData = useSelector((state) => state.returnMap.returnMapDefinition);


  useEffect(() => {
  
    setDataList([returnMapDefinitionData])
  }, [returnMapDefinitionData]);



Uncaught TypeError: Cannot read properties of null (reading 'children')
    at Table.getTotalDataWithChildren (Table.js:1317:1)
    at Table.renderPagination (Table.js:1350:1)
    at Table.render (Table.js:1537:1)
    at Yv (react-dom.development.min.js:1:167672)
    at Qv (react-dom.development.min.js:1:167226)
    at dg (react-dom.development.min.js:1:180441)
    at HTMLUnknownElement.e (react-dom.development.min.js:1:2586)
    at Object.t (react-dom.development.min.js:1:2630)
    at g (react-dom.development.min.js:1:3531)
    at gb (react-dom.development.min.js:1:216332)
    at tb (react-dom.development.min.js:1:210314)
    at Jy (react-dom.development.min.js:1:210157)
    at jy (react-dom.development.min.js:1:208626)
    at react-dom.development.min.js:1:104879
    at unstable_runWithPriority (react.development.js:2685:14)
    at ff (react-dom.development.min.js:1:104577)
    at vf (react-dom.development.min.js:1:104817)
    at hf (react-dom.development.min.js:1:104752)
    at cb (react-dom.development.min.js:1:215536)
    at unstable_runWithPriority (react.development.js:2685:14)
    at ff (react-dom.development.min.js:1:104577)
    at sb (react-dom.development.min.js:1:215132)
    at react-dom.development.min.js:1:214412
    at workLoop (react.development.js:2629:36)
    at flushWork (react.development.js:2584:18)
    at MessagePort.performWorkUntilDeadline (react.development.js:2196:29)
getTotalDataWithChildren @ Table.js:1317
renderPagination @ Table.js:1350
render @ Table.js:1537
Yv @ react-dom.development.min.js:1
Qv @ react-dom.development.min.js:1
dg @ react-dom.development.min.js:1
e @ react-dom.development.min.js:1
t @ react-dom.development.min.js:1
g @ react-dom.development.min.js:1
gb @ react-dom.development.min.js:1
tb @ react-dom.development.min.js:1
Jy @ react-dom.development.min.js:1
jy @ react-dom.development.min.js:1
(anonymous) @ react-dom.development.min.js:1
unstable_runWithPriority @ react.development.js:2685
ff @ react-dom.development.min.js:1
vf @ react-dom.development.min.js:1
hf @ react-dom.development.min.js:1
cb @ react-dom.development.min.js:1
unstable_runWithPriority @ react.development.js:2685
ff @ react-dom.development.min.js:1
sb @ react-dom.development.min.js:1
(anonymous) @ react-dom.development.min.js:1
workLoop @ react.development.js:2629
flushWork @ react.development.js:2584
performWorkUntilDeadline @ react.development.js:2196
Show 3 more frames
Show less
react-dom.development.min.js:1  The above error occurred in the <Table> component:
    in Table
    in Unknown
    in Unknown (created by Context.Consumer)
    in LocaleReceiver (created by Context.Consumer)
    in YkbUiThemeConsumer (created by Table)
    in Table (at ReturnMapDefinitionServiceParametersTable.js:198)
    in ReturnMapDefinitionServiceParametersTable (at ReturnMapServiceParameters.js:220)
    in div (created by styled.div)
    in styled.div (created by Context.Consumer)
    in YkbUiThemeConsumer (created by Col)
    in Col (at ReturnMapServiceParameters.js:219)
    in div (created by styled.div)
    in styled.div (created by Context.Consumer)
    in YkbUiThemeConsumer (created by Row)
    in Row (at ReturnMapServiceParameters.js:218)
    in ReturnMapServiceParameters (at ReturnMapServiceParametersWrapper.js:10)
    in Provider (at ReturnMapServiceParametersWrapper.js:9)
    in ReturnMapServiceParametersWrapper (created by AsyncComponent)
    in AsyncComponent (created by _class)
    in ExternalDataContextProvider (created by _class)
    in _class (created by SparxPage)
    in ShellCommunicatorProvider (created by SparxPage)
    in PermissionProvider (created by SparxPage)
    in IntlProvider (created by SparxPage)
    in SparxPage (created by Route)
    in Route (created by RouteCreator)
    in div (created by RouteCreator)
    in Router (created by MemoryRouter)
    in MemoryRouter (created by RouteCreator)
    in div (created by RouteCreator)
    in RouteCreator
    in Le (created by YkbUiThemeProvider)
    in YkbUiThemeProvider
    in me

React will try to recreate this component tree from scratch using the error boundary you provided, _class.
