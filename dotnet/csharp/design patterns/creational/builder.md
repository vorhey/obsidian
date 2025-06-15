```csharp
var amb = new AndroidBuilder();

var p1 = amb.SetModel("S21 Galaxy").Build();

Console.WriteLine(p1);

public enum Battery
{
    None,
    Mha1500,
    Mha2000,
    Mha3000,
}

public enum BaseOs
{
    None,
    Android,
    Ios,
}

public enum Color
{
    None,
    Blue,
    Green,
    Red,
    White,
}

public record Mobile
{
    public string Model { get; set; } = default!;
    public Battery Battery { get; set; }
    public BaseOs BaseOs { get; set; }
    public Color Color { get; set; }
}

public abstract class MobileBuilder
{
    protected Mobile Mobile = new();
    public abstract Mobile Build();
    public abstract MobileBuilder SetModel(string model);
    public abstract MobileBuilder SetBattery(Battery battery);
    public abstract MobileBuilder SetBaseOs(BaseOs baseOs);
    public abstract MobileBuilder SetColor(Color color);
}

public sealed class AndroidBuilder : MobileBuilder
{
    public override Mobile Build()
    {
        ArgumentNullException.ThrowIfNullOrEmpty(Mobile.Model);

        if (Mobile.BaseOs == BaseOs.None)
        {
            Mobile.BaseOs = BaseOs.Android;
        }

        if (Mobile.Color == Color.None)
        {
            Mobile.Color = Color.White;
        }

        if (Mobile.Battery == Battery.None)
        {
            Mobile.Battery = Battery.Mha2000;
        }

        return Mobile;
    }

    public override MobileBuilder SetBaseOs(BaseOs baseOperatingSystem)
    {
        Mobile.BaseOs = baseOperatingSystem;
        return this;
    }

    public override MobileBuilder SetBattery(Battery battery)
    {
        Mobile.Battery = battery;
        return this;
    }

    public override MobileBuilder SetColor(Color color)
    {
        Mobile.Color = color;
        return this;
    }

    public override MobileBuilder SetModel(string model)
    {
        Mobile.Model = model;
        return this;
    }
}
```