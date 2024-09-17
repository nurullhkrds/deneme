const CloseTabButton = () => {
  const closeTab = () => {
    // Sekmeyi kapatmayı dener
    window.close();
  };

  return (
    <button onClick={closeTab}>
      Bu Sekmeyi Kapat
    </button>
  );
};
