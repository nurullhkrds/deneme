import React, { useEffect, useState } from "react";
import { Select } from "antd";

const { Option } = Select;

export default function KurumSecim() {
  const [institutionList, setInstitutionList] = useState([]);
  const [loading, setLoading] = useState(false);

  useEffect(() => {
    getInstitutionList();
  }, []);

  const getInstitutionList = async () => {
    setLoading(true);

    try {
      // Gerçek servis burası olacak
      // const response = await kurumServisiGetir();

      // örnek mock response
      const response = [
        {
          id: 1,
          kurumAdi: "AKŞEHİR BELEDİYESİ SU",
          kurumTipi: "Su",
          il: "KONYA",
        },
        {
          id: 2,
          kurumAdi: "ALAŞEHİR BELEDİYESİ SU",
          kurumTipi: "Su",
          il: "MANİSA",
        },
        {
          id: 3,
          kurumAdi: "ARASEDAŞ YENİ",
          kurumTipi: "Elektrik",
          il: "AĞRI",
        },
        {
          id: 4,
          kurumAdi: "ARASEDAŞ YENİ",
          kurumTipi: "Elektrik",
          il: "ARDAHAN",
        },
        {
          id: 5,
          kurumAdi: "ARASEDAŞ YENİ",
          kurumTipi: "Elektrik",
          il: "BAYBURT",
        },
      ];

      setInstitutionList(response);
    } catch (error) {
      console.error("Kurum listesi alınamadı", error);
    } finally {
      setLoading(false);
    }
  };

  const handleChange = (value, option) => {
    console.log("Seçilen kurum:", option?.data);
  };

  return (
    <div style={{ width: 700 }}>
      <Select
        showSearch
        placeholder="Kurum ara"
        style={{ width: "100%" }}
        loading={loading}
        optionLabelProp="label"
        onChange={handleChange}
        filterOption={(input, option) => {
          const item = option?.data;
          if (!item) return false;

          const searchText = input.toLocaleLowerCase("tr");

          return (
            item.kurumAdi?.toLocaleLowerCase("tr").includes(searchText) ||
            item.kurumTipi?.toLocaleLowerCase("tr").includes(searchText) ||
            item.il?.toLocaleLowerCase("tr").includes(searchText)
          );
        }}
      >
        {institutionList.map((item) => (
          <Option
            key={`${item.id}-${item.il}`}
            value={item.id}
            label={item.kurumAdi}
            data={item}
          >
            <div
              style={{
                display: "grid",
                gridTemplateColumns: "3fr 1.5fr 1fr",
                gap: "12px",
                alignItems: "center",
              }}
            >
              <span>{item.kurumAdi}</span>
              <span>{item.kurumTipi}</span>
              <span>{item.il}</span>
            </div>
          </Option>
        ))}
      </Select>
    </div>
  );
}
