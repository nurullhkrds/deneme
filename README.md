 @OneToMany(mappedBy = "returnMapDefinition", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<ReturnMap> returnMaps = new ArrayList<>();


  @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "return_map_definition_id", nullable = false)
    private ReturnMapDefinition returnMapDefinition;
