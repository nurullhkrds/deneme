import React, { useEffect, useRef, useState } from "react";
import { AutoComplete } from "ykb-ui";
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
        { id: 1, kurumAdi: "BEYGAZ/BİLECİK-BOLU D.GAZ", kurumTipi: "Doğalgaz", il: "BİLECİK" },
        { id: 2, kurumAdi: "BEYGAZ/BİLECİK-BOLU D.GAZ", kurumTipi: "Doğalgaz", il: "BOLU" },
        { id: 3, kurumAdi: "BİLECİK BELEDİYESİ SU", kurumTipi: "Su", il: "BİLECİK" },
        { id: 4, kurumAdi: "OSDAŞ ONLINE BİLECİK", kurumTipi: "Elektrik", il: "BİLECİK" },
        { id: 5, kurumAdi: "TEDAŞ ONLINE BİLECİK", kurumTipi: "Elektrik", il: "BİLECİK" }
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

    const headerOption = {
      value: "__header__",
      disabled: true,
      label: (
        <div className="institution-dropdown-header-row">
          <div className="institution-header-col kurum-adi">Kurum Adı</div>
          <div className="institution-header-col kurum-tipi">Kurum Tipi</div>
          <div className="institution-header-col il">İl</div>
        </div>
      )
    };

    const dataOptions = filtered.map((item) => ({
      value: item.kurumAdi,
      key: item.id,
      data: item,
      label: (
        <div className="institution-option-row">
          <div className="institution-option-col kurum-adi">{item.kurumAdi}</div>
          <div className="institution-option-col kurum-tipi">{item.kurumTipi}</div>
          <div className="institution-option-col il">{item.il}</div>
        </div>
      )
    }));

    setOptions([headerOption, ...dataOptions]);
  };

  const onSelect = (val, option) => {
    if (val === "__header__") return;

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
    <div ref={wrapperRef} className="institution-search-container">
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
        popupClassName="institution-autocomplete-popup"
      />

      {selected && (
        <div className="selected-institution-box">
          <div><strong>Kurum Adı:</strong> {selected.kurumAdi}</div>
          <div><strong>Kurum Tipi:</strong> {selected.kurumTipi}</div>
          <div><strong>İl:</strong> {selected.il}</div>
        </div>
      )}
    </div>
  );
}





.institution-search-container {
  width: 740px;
  position: relative;
}

.institution-autocomplete-popup .ant-select-item {
  padding: 0 !important;
}

.institution-autocomplete-popup .ant-select-item-option-content {
  padding: 0 !important;
}

.institution-dropdown-header-row {
  display: grid;
  grid-template-columns: 2.4fr 1.2fr 1fr;
  align-items: center;
  background: #1e98e6;
  color: #fff;
  font-weight: 700;
  font-size: 15px;
  min-height: 56px;
  padding: 0 16px;
  pointer-events: none;
}

.institution-header-col {
  padding: 16px 12px;
  border-right: 1px solid rgba(255, 255, 255, 0.25);
}

.institution-header-col:last-child {
  border-right: none;
}

.institution-option-row {
  display: grid;
  grid-template-columns: 2.4fr 1.2fr 1fr;
  align-items: center;
  min-height: 56px;
  padding: 0 16px;
  background: #fff;
  transition: background-color 0.2s ease;
}

.institution-option-col {
  padding: 16px 12px;
  font-size: 15px;
  color: #1f1f1f;
  border-right: 1px solid #edf1f5;
}

.institution-option-col:last-child {
  border-right: none;
}

/* hover rengi */
.institution-autocomplete-popup .ant-select-item-option-active .institution-option-row {
  background: #e8f4ff !important;
}

/* seçili satır */
.institution-autocomplete-popup .ant-select-item-option-selected .institution-option-row {
  background: #d8eeff !important;
}

/* header satırı hover almasın */
.institution-autocomplete-popup .ant-select-item-option-disabled {
  cursor: default !important;
  background: transparent !important;
}

.selected-institution-box {
  margin-top: 16px;
  padding: 14px 16px;
  border: 1px solid #d9e2ec;
  border-radius: 8px;
  background: #f8fbff;
  font-size: 14px;
  line-height: 1.7;
}
