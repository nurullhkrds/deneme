<img width="1402" height="526" alt="{86A7FBF1-1080-4571-A471-3171E4B37616}" src="https://github.com/user-attachments/assets/6f1bbd1b-a1a6-473c-b489-1b7553aa922f" />
<img width="1198" height="263" alt="image" src="https://github.com/user-attachments/assets/8353e48c-5e30-4519-b7bf-82591e27e7d1" />




import React, { useEffect, useRef, useState } from "react";
import { AutoComplete, Tag } from "ykb-ui";
import "./styles/SearchInstitution.css";



export default function SearchInstitution() {
  const [institutionList, setInstitutionList] = useState([]);
  const [loading, setLoading] = useState(false);
  const [options, setOptions] = useState([]);
  const [selected, setSelected] = useState(null);
  const wrapperRef = useRef(null);

  useEffect(() => {
    getInstitutionList();
  }, []);

  const getInstitutionList = async () => {
    setLoading(true);
    try {
      const response = [
        { id: 1, kurumAdi: "AKŞEHİR BELEDİYESİ SU", kurumTipi: "Su", il: "KONYA" },
        { id: 2, kurumAdi: "ALAŞEHİR BELEDİYESİ SU", kurumTipi: "Su", il: "MANİSA" },
        { id: 3, kurumAdi: "ARASEDAŞ YENİ", kurumTipi: "Elektrik", il: "AĞRI" },
        { id: 4, kurumAdi: "ARASEDAŞ YENİ", kurumTipi: "Elektrik", il: "ARDAHAN" },
        { id: 5, kurumAdi: "ARASEDAŞ YENİ", kurumTipi: "Elektrik", il: "BAYBURT" },
      ];
      setInstitutionList(response);
    } catch (error) {
      console.error("Kurum listesi alınamadı", error);
    } finally {
      setLoading(false);
    }
  };

  const onSearch = (searchText) => {
    if (!searchText.trim()) {
      setOptions([]);
      return;
    }
    const q = searchText.toLocaleLowerCase("tr");
    const filtered = institutionList.filter(
      (item) =>
        item.kurumAdi.toLocaleLowerCase("tr").includes(q) ||
        item.kurumTipi.toLocaleLowerCase("tr").includes(q) ||
        item.il.toLocaleLowerCase("tr").includes(q)
    );
    setOptions(
      filtered.map((item) => ({
        value: item.kurumAdi,
        data: item,
      
      }))
    );
  };

  const onSelect = (val, option) => {
    setSelected(option.data);
    console.log("Seçilen kurum:", option.data);
  };

  const onChange = (val) => {
    if (!val) {
      setSelected(null);
      setOptions([]);
    }
  };

  return (
    <div ref={wrapperRef} style={{ width: 600 }}>
      <AutoComplete
        options={options}
        onSearch={onSearch}
        onSelect={onSelect}
        onChange={onChange}
        placeholder="Kurum adı, tipi veya il ile arayın..."
        loading={loading}
        allowClear
        style={{ width: "100%" }}
        notFoundContent="Sonuç bulunamadı"
        
      />


    </div>
  );
}



