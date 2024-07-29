import React, { useState } from 'react';
import { Button, Modal, Message, Icon } from 'some-ui-library'; // UI kütüphanesini uygun şekilde içe aktarın

const ModalExample = () => {
  const [visible, setVisible] = useState(false);

  const handleClickPromptCustomButtonText = () => {
    Message.prompt({
      title: 'prompt title',
      content: 'prompt modal',
      icon: <Icon name="warning-circle" colorType="warning" />,
      onClose: () => {
        console.log('onClose');
      },
      onOk: () => {
        console.log('onOk');
      },
      onCancel: () => {
        console.log('onCancel');
      },
      okText: 'Done',
      cancelText: 'Return',
    });
  };

  const handleClose = () => {
    setVisible(false);
  };

  return (
    <>
      <Button
        onClick={handleClickPromptCustomButtonText}
        type="primary"
        style={{ margin: 8 }}
      >
        custom button text prompt
      </Button>

      <Modal
        visible={visible}
        title="Title"
        onClose={handleClose}
      >
        <h4>Text in a modal</h4>
        <p>
          Duis mollis, est non commodo luctus, nisi erat porttitor ligula.
        </p>
        <Button onClick={handleClose}>Close</Button>
      </Modal>
    </>
  );
};

export default ModalExample;
