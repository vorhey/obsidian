```csharp
using System;
using System.Collections;
using System.Collections.Generic;

namespace SandboxConsole
{
    public class Program
    {
        static void Main(string[] args)
        {
            var photoEngine = new PhotoProcessor();
            
            var filters = new Filters();

            photoEngine.RegisterFilter(new ContrastFilter());

            photoEngine.RegisterFilter(new BrightnessFilter());

            PhotoProcessor.ApplyFilter filterApplier = filters.BrightnessFilter;

            filterApplier += filters.ContrastFilter;

            photoEngine.ProcessMultiple("C:", filters.BrightnessFilter, filters.ContrastFilter);

            Console.ReadKey();
        }

    }

    public class Photo
    {
        public static Photo Load(string path)
        {
            return new Photo();
        }

        public static void Save()
        {

        }
    }

    public class PhotoProcessor
    {
        public delegate void ApplyFilter(Photo photo);

        private readonly IList<IFilter> _filters = new List<IFilter>();

        public void Process(string path, ApplyFilter filterHandler)
        {
            var photo = Photo.Load(path);

            foreach (var filter in _filters)
            {
                filter.ApplyFilter(photo);
            }

            filterHandler(photo);
        }

        public void ProcessMultiple(string path, params Action<Photo>[] actions)
        {
            var photo = Photo.Load(path);

            foreach (var action in actions)
            {
                action(photo);
            }
        }

        public void RegisterFilter(IFilter filter)
        {
            _filters.Add(filter);
        }
    }

    public interface IFilter
    {
        void ApplyFilter(Photo photo);
    }

    public class BrightnessFilter : IFilter
    {
        public void ApplyFilter(Photo photo)
        {
            Console.WriteLine("Brightness Filter");
        }
    }

    public class ContrastFilter : IFilter
    {
        public void ApplyFilter(Photo photo)
        {
            Console.WriteLine("Contrast Filter");
        }
    }

    public class Filters
    {
        public void BrightnessFilter(Photo photo) => Console.WriteLine("Brightness Filter");
        public void ContrastFilter(Photo photo) => Console.WriteLine("Contrast Filter");
    }
}

```