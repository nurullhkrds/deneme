
     <Button
            onClick={handleClickPromptCustomButtonText}
            type="primary"
            style={{ margin: 8 }}>
            custom button text prompt
          </Button>


        <Modal
          visible={this.state.visible}
          title="Title"
          onClose={this.onClick}>
          <h4>Text in a modal</h4>
          <p>
            Duis mollis, est non commodo luctus, nisi erat porttitor ligula.
          </p>
          <Button onClick={this.onClick}>Close</Button>
        </Modal>

const handleClickPromptCustomButtonText = event => {
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

class ModalExample extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      visible: false,
    };
    this.onClick = this.onClick.bind(this);
  }

  onClick() {
    this.setState({
      visible: false,
    });
  }

