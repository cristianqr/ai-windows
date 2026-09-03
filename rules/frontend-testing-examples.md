# Frontend Testing — Examples

Companion to `frontend-testing.mdc`.

## Selector helpers

Keep `wrapper` and selector helpers in the outermost `describe`:

```javascript
describe("MyComponent", () => {
  let wrapper;

  const pageHeading = () =>
    wrapper.find('[data-test-id="page-heading"]');
  const submitButton = () =>
    wrapper.find('[data-test-id="submit-button"]');
  const tab = (key) => wrapper.find(`[data-test-id="tab-${key}"]`);

  const mountComponent = (props = {}) => {
    wrapper = mount(MyComponent, { props });
    return wrapper;
  };

  beforeEach(() => {
    mountComponent();
  });

  it("renders the page heading", () => {
    expect(pageHeading().text()).toBe("Expected title");
  });

  describe("when the submit button is clicked", () => {
    it("emits the submit event", async () => {
      await submitButton().trigger("click");

      expect(wrapper.emitted("submit")).toBeTruthy();
    });
  });

  describe("when rendered with alternate props", () => {
    beforeEach(() => {
      mountComponent({ disabled: true });
    });

    it("does not render the submit button", () => {
      expect(submitButton().exists()).toBe(false);
    });
  });
});
```

## BDD naming

Ideal `describe` / `it` structuring:

```javascript
describe('Application', () => {
  describe('Initial Rendering', () => {
    it('should create the application');
    it('should render the initial UI as expected');
    it("should display the title 'angular-unit-test-example'");
    it('should display the correct label');
    it('should render the title element');
  });

  describe('Clicking the greeting button', () => {
    it('should render "Hello World!"');
    it('should clear the greeting input field');
    it('should focus the greeting input field');
  });
});

describe('Matches List', () => {
  describe('when data is fetched from the API', () => {
    it('should render the matches list');
  });

  describe('when the API returns no data', () => {
    it('should display a "no results found" message');
  });
});

describe('Payment Form Validation', () => {
  describe('Cardholder Name', () => {
    it('should require a cardholder name');
    it('should allow alphabetic characters only');
  });

  describe('Card Number', () => {
    it('should require a card number');
    it('should validate that the card number is a 16-digit number');
  });

  describe('Expiry Month', () => {
    it('should require an expiry month');
    it('should validate that the month is between 1 and 12');
  });

  describe('Expiry Year', () => {
    it('should require an expiry year');
    it('should validate that the year is four digits and within the allowed range');
  });

  describe('CVV', () => {
    it('should require a CVV');
    it('should validate that the CVV is a 3-digit number');
  });

  describe('Form Submission', () => {
    it('should enable the submit button when all inputs are valid and no errors are present');
  });
});

describe('Task Board', () => {
  describe('Clicking the create task button', () => {
    it('should add a task to stage 0');
    it('should not create a task when the input is empty');
  });

  describe('Moving tasks between stages', () => {
    it('should disable the backward icon and enable the forward icon for tasks in stage 0');
    it('should allow tasks to move forward from stage 0 to stage 4 with correct icon states');
    it('should allow tasks to move backward from stage 4 to stage 0 with correct icon states');
    it('should maintain the correct task state after multiple forward and backward operations');
  });

  describe('Clicking the delete task button', () => {
    it('should delete a task regardless of its stage');
    it('should maintain the correct task state after multiple moves and deletions');
  });
});

describe('StrengthPipe', () => {
  it('should display "weak" when strength is 5');
  it('should display "strong" when strength is 10');
});

describe('Image Details Page', () => {
  describe('before data loads', () => {
    it('should display a loading indicator');
  });

  describe('when data loads successfully', () => {
    it('should render the heading with the image ID');
    it('should render the image');

    describe('Breeds', () => {
      it('should display breeds if available');
      it('should display "unknown" if no breeds exist');
    });
  });

  describe('when there is an error loading data', () => {
    it('should render an error alert');
  });
});

describe('Favorite Section', () => {
  describe('when the image is not favorited', () => {
    it('should show "Not Favorited"');
    it('should show a non-red favorite button');
    it('should disable the delete favorite button');
    it('should call the API when the favorite button is clicked');
  });

  describe('when the image is favorited', () => {
    it('should show "Favorited"');
    it('should display the favorite button in red');
    it('should enable the delete favorite button');
    it('should call the API when the delete button is clicked');
  });
});

describe('Vote Section', () => {
  describe('when the image has not been voted on', () => {
    it('should show "Not Voted"');
    it('should show default button styles');
  });

  describe('when the image is upvoted', () => {
    it('should show "Upvoted"');
    it('should highlight the upvote button');
  });

  describe('when the image is downvoted', () => {
    it('should show "Downvoted"');
    it('should highlight the downvote button');
  });

  it('should call the API when upvote is clicked');
  it('should call the API when downvote is clicked');
});

describe('ImageList', () => {
  describe('Table Rows', () => {
    describe('when data loads successfully', () => {
      it('should render one row for each dog returned by the API');
      it('should render the image');
      it('should render breeds if available');
      it('should render "Unknown" when no breeds exist');
      it('should navigate to the details page when a row is clicked');
    });
  });

  describe('Pagination', () => {
    describe('when data loads successfully', () => {
      it('should render pagination controls');
      it('should fetch the next page when next is clicked');
      it('should fetch the previous page when previous is clicked');
      it('should update the URL when pagination changes');
    });
  });

  describe('Error State', () => {
    it('should render an error alert when data loading fails');
  });
});

describe('HeroService', () => {
  describe('getHero', () => {
    it('should call the API with the correct URL');
    it('should call the API with the correct method');
    it('should call the API with the correct query parameters');
  });

  describe('saveHero', () => {
    it('should call the API with the correct URL');
    it('should call the API with the correct method');
    it('should call the API with the correct payload');
  });
});
```

## HTTP assertion examples

```javascript
expect(axios.post).toHaveBeenCalledWith(
  '/api/resource',
  expect.objectContaining({ id: '123', status: 'active' })
);

axios.get.mockResolvedValueOnce({ data: { name: 'Alice' } });
wrapper.find('[data-test-id="load-button"]').trigger('click');
await flushPromises();
expect(wrapper.find('[data-test-id="user-name"]').text()).toBe('Alice');
```
