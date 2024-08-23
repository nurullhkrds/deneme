  const url = `https://api.example.com/data?query=${inputValue}`;  // API URL'ini kendi URL'inizle değiştirin

    fetch(url)
      .then(response => {
        if (!response.ok) {
          throw new Error('Bir hata oluştu: ' + response.status);
        }
        return response.json();  // JSON formatında bir yanıt bekleniyor
      })
      .then(data => {
        setData(data);  // Yanıtı state'e kaydediyoruz
      })
      .catch(error => {
        setError(error.message);  // Hata durumunda hatayı state'e kaydediyoruz
      });
