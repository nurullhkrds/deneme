 // Eğer Redux'tan gelen subTableActive state henüz gelmediyse, boş render et
  if (subTableActive === null || subTableActive === undefined) {
    return null; // İlk renderda boş döner
  }
