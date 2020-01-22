# state-machine
Simple finite-state machine written for my project toolbar for the added ability to pass arguments to the transition actions.

State machine takes a definition object in the following format;
```
{
  initValue: <value>, //value to initialize for the state machine
  <state>: {  //state definition
    actions: {  //methods defined for state
      onEnter() {},
      onExit() {}
      },
    transitions: { //transition methods defined for each target
      <event>: {  //event definition
        target: <state>, //target state for the method called
        action() {} //method called when the transition occures
        }
    }
}
```


Example usage:
```
modeStateMachine = StateMachine(modeStateMachineDef);
modeStateMachine.transition(initMode);
modeStateMachine.is() //returns current state

var modeStateMachineDef = {
  initValue: "init",
  init: {
    actions: {
      onEnter() {},
      onExit() {
        animations.showBarTween({ barElement: bar.getNodes().barElement });
      }
    },
    transitions: {
      small: {
        target: "small",
        action() {
          barModeAnimations.seek("small");
        }
      },
      large: {
        target: "large",
        action() {
          barModeAnimations.seek("large");
        }
      },
      detail: {
        target: "detail",
        action() {
          barModeAnimations.seek("detail");
          _setEventScroll();
          eventScrollAnimations.tl.progress(0, false);
        }
      },
      modal: {
        target: "modal",
        action() {}
      }
    }
  },
  small: {
    actions: {
      onEnter() {},
      onExit() {}
    },
    transitions: {
      large: {
        target: "large",
        action() {
          _setModeLarge();
        }
      },
      modal: {
        target: "modal",
        action(opts) {
          _setModeModal(opts);
          _setEventScroll();
          eventScrollAnimations.tl.progress(0, false);
        }
      }
    }
  },
  large: {
    actions: {
      onEnter() {},
      onExit() {}
    },
    transitions: {
      small: {
        target: "small",
        action() {
          _setModeSmall();
        }
      },
      detail: {
        target: "detail",
        action() {
          _setModeDetail();
          _setEventScroll();
          eventScrollAnimations.tl.progress(0, false);
        }
      }
    }
  },
  detail: {
    actions: {
      onEnter() {},
      onExit() {}
    },
    transitions: {
      small: {
        target: "large",
        action() {
          _killEventScrollAnimations();
          _setModeSmall(true);
        }
      },
      large: {
        target: "large",
        action() {
          _killEventScrollAnimations();
          _setModeLarge();
        }
      },
      modal: {
        target: "modal",
        action(opts) {
          _setModeModal(opts);
        }
      }
    }
  },
  modal: {
    actions: {
      onEnter() {},
      onExit() {
        if (openedElements.event) openedElements.event.close();
        openedElements.modal.reverse();
        openedElements.modal = null;
      }
    },
    transitions: {
      small: {
        target: "small",
        action() {
          _killEventScrollAnimations();
        }
      },
      detail: {
        target: "detail",
        action() {}
      }
    }
  }
};
```
