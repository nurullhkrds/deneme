@Service
public class AdminCityServiceImpl implements AdminCityService {

    private final CityRepository cityRepository;
    private final AdminCityMapper cityMapper;

    public AdminCityServiceImpl(CityRepository cityRepository, AdminCityMapper cityMapper) {
        this.cityRepository = cityRepository;
        this.cityMapper = cityMapper;
    }


    @Override
    public CityDTO getCityByCode(String code) throws MicroException{
        City city = cityRepository.findByCode(code);
        if (city == null){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.CITY_NOT_FOUND);

        }
        CityDTO dto= cityMapper.toCityDTO(city);
        return dto;
    }

}
