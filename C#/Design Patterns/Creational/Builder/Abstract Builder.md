```csharp
namespace Sandbox
{
    public record Mobile
    {
        public string Model { get; init; } = default!;
        public string Color { get; init; } = default!;
        public ScreenType ScreenType { get; init; }
        public Battery Battery { get; init; }
        public BaseOS BaseOS { get; init; }
    }

    public enum ScreenType
    {
        None,
        Gorilla,
        Saphire,
    }

    public enum Battery
    {
        None,
        Mha1500,
        Mha3000,
    }

    public enum BaseOS
    {
        None,
        Ios,
        Android,
    }

    public abstract class MobileBuilder
    {
        protected string Model = default!;

        protected string Color = default!;

        protected ScreenType ScreenType;

        protected Battery Battery;

        protected BaseOS BaseOS;

        public MobileBuilder SetModel(string model)
        {
            Model = model;
            return this;
        }

        public MobileBuilder SetColor(string color)
        {
            Color = color;
            return this;
        }

        public MobileBuilder SetScreen(ScreenType screenType)
        {
            ScreenType = screenType;
            return this;
        }

        public MobileBuilder SetBattery(Battery battery)
        {
            Battery = battery;
            return this;
        }

        public MobileBuilder SetBaseOS(BaseOS os)
        {
            BaseOS = os;
            return this;
        }

        public abstract Mobile Build();
    }

    public class AndroidBuilder : MobileBuilder
    {
        public override Mobile Build()
        {
            ArgumentNullException.ThrowIfNullOrEmpty(Model);

            if (string.IsNullOrEmpty(Color))
            {
                Color = "white";
            }

            if (ScreenType == ScreenType.None)
            {
                ScreenType = ScreenType.Gorilla;
            }

            if (Battery == Battery.None)
            {
                Battery = Battery.Mha1500;
            }

            if (BaseOS == BaseOS.None)
            {
                BaseOS = BaseOS.Android;
            }

            return new Mobile
            {
                Model = Model,
                Color = Color,
                ScreenType = ScreenType,
                Battery = Battery,
                BaseOS = BaseOS,
            };
        }
    }
}
```