class ModalExample extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      visible: false,
    };
    this.showModal = this.showModal.bind(this);
    this.handleOk = this.handleOk.bind(this);
  }

  showModal() {
    this.setState({
      visible: true,
    });
  }

  handleOk(e) {
    this.setState({
      visible: false,
    });
  }

  render() {
    return (
      <div>
        <Button type="primary" onClick={this.showModal}>
          Open
        </Button>
        <Modal
          title="Basic Modal"
          visible={this.state.visible}
          onClose={this.handleOk}>
          <div>Some contents...</div>
          <div>Some contents...</div>
          <div>Some contents...</div>
        </Modal>
      </div>
    );
  }
}
<ModalExample />;
