@Service
@RequiredArgsConstructor
public class ProvisionNextServiceImpl implements ProvisionNextService{

    private final ProvisionNextClient provisionNextClient;

    @Override
    public MakeProvisionResponse makeProvision(MakeProvisionRequest request) {
        return provisionNextClient.makeProvision(request);
    }

	@Override
	public MakeReverseProvisionResponse makeReverseProvision(MakeReverseProvisionRequest request) {
		return provisionNextClient.makeReverseProvision(request);
	}
    
    
}
