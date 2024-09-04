/* Select elementine stil ver */
.custom-select {
  width: 100%;
  padding: 8px;
  border-radius: 4px;
  border: 1px solid #d9d9d9; /* Ant Design'daki gri kenarlık */
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1); /* Hafif gölge */
  transition: border-color 0.3s ease; /* Geçiş efekti */
  background-color: #fff; /* Beyaz arka plan */
  font-size: 14px; /* Font boyutu */
  outline: none; /* Focus olduğunda dış çizgi olmasın */
  -webkit-appearance: none; /* Tarayıcı varsayılan stilini kaldır */
  -moz-appearance: none; /* Tarayıcı varsayılan stilini kaldır */
}

/* Select elementinin üzerine gelindiğinde */
.custom-select:hover {
  border-color: #40a9ff; /* Ant Design'da hover olduğunda mavi renkte kenarlık */
}

/* Option elementlerine genel stil ver */
.custom-select option {
  padding: 10px; /* Dikey ve yatay boşluk */
  font-size: 14px;
  background-color: #fff;
  color: #000;
  border: none; /* Kenarlık olmadan */
}

/* Seçili option stilini ayarla */
.custom-select option:checked {
  background-color: #e6f7ff; /* Ant Design'daki mavi arka plan */
  color: #1890ff; /* Ant Design'daki seçili yazı rengi */
}

/* Option hover stili */
.custom-select option:hover {
  background-color: #f5f5f5; /* Hover için açık gri arka plan */
  color: #000; /* Hover olduğunda siyah yazı */
}
