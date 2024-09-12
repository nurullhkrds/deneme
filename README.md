  useEffect(() => {
    const returnMapCodeDefSelect = definitionListIsActiveTrue.find((item) => item.returnMapCode === returnMapCodeSearch)
    console.log(returnMapCodeDefSelect)
  }, [returnMapCodeSearch, definitionListIsActiveTrue])
