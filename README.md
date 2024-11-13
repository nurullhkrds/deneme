import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.util.StringUtils;

class AdminChannelServiceImplTest {

    @Mock
    private ChannelRepository channelRepository;

    @Mock
    private AdminChannelMapper mapper;

    @InjectMocks
    private AdminChannelServiceImpl adminChannelService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void findChannelByChannelCode_shouldReturnNull_whenChannelCodeIsEmpty() {
        String channelCode = "";
        ChannelDTO result = adminChannelService.findChannelByChannelCode(channelCode);
        assertNull(result);
    }

    @Test
    void findChannelByChannelCode_shouldReturnChannelDTO_whenChannelCodeIsValid() {
        String channelCode = "validChannelCode";
        Channel channel = new Channel();
        ChannelDTO channelDTO = new ChannelDTO();

        when(channelRepository.findByCode(channelCode)).thenReturn(channel);
        when(mapper.toDto(channel)).thenReturn(channelDTO);

        ChannelDTO result = adminChannelService.findChannelByChannelCode(channelCode);
        assertNotNull(result);
        assertEquals(channelDTO, result);

        verify(channelRepository, times(1)).findByCode(channelCode);
        verify(mapper, times(1)).toDto(channel);
    }

    @Test
    void findChannelByChannelCode_shouldReturnNull_whenChannelNotFound() {
        String channelCode = "nonExistentChannelCode";

        when(channelRepository.findByCode(channelCode)).thenReturn(null);

        ChannelDTO result = adminChannelService.findChannelByChannelCode(channelCode);
        assertNull(result);

        verify(channelRepository, times(1)).findByCode(channelCode);
        verify(mapper, never()).toDto(any());
    }
} 
