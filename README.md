  const [warning, setWarning] = useState('');
    if (parseFloat(value) < sum) {
      setWarning(`Bu değer ${sum} değerinden küçük olamaz.`);
    } else {
      setWarning(''); // Uyarıyı temizle
    }


        {warning && <p style={{ color: 'red' }}>{warning}</p>}
